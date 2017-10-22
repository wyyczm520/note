# docker创建私有库

### 1. 环境
**环境：**centos7

**IP地址：**10.211.55.30

**dockere版本：**1.10.3

**镜像仓库版本：**V2

### 2. 搭建私有仓库

首先在136机器上下载registry镜像

<pre>$ docker pull registry</pre>

下载完之后我们通过该镜像启动一个容器

<pre>$ docker run -d -p 5000:5000 registry</pre>

默认情况下，会将仓库存放于容器内的/tmp/registry目录下，这样如果容器被删除，则存放于容器中的镜像也会丢失，所以我们一般情况下会指定本地一个目录挂载到容器内的/tmp/registry下，我将/opt/data/registry目录挂载到/tmp/registry目录下，如果你本地没有这个目录需要新创建，同时需要给/opt/data/registry目录扩大权限chmod +777 /opt/data/registry。

此处有坑：默认情况下是在容器内的/tmp/registry目录下，但是我的容器镜像是存放在容器中的/var/lib/registry  这个位置。
我是搭建完毕之后，上传一个镜像之后然后使用 find / -name ***查到的位置

<pre>
[root@server01 ~]# docker run -d -p 5000:5000 -v /opt/data/registry:/var/lib/registry registry
 55c60589cb0e2d094d5371c4dd650127cfeae1b361477d50cfe48552e6308830
</pre>

可以看到我们启动了一个容器，地址为：10.211.55.30:5000。

### 3. 测试
接下来我们就要操作把一个本地镜像push到私有仓库中。首先在10.211.55.30机器下pull一个比较小的镜像来测试（此处使用的是busybox）。

<pre>$ sudo docker pull busybox</pre>

接下来修改一下该镜像的tag，镜像的格式为  镜像仓库IP：端口/镜像名称

<pre>$ sudo docker tag busybox 10.211.55.30:5000/busybox</pre>

接下来把打了tag的镜像上传到私有仓库。

<pre>$ sudo docker push 10.211.55.30:5000/busybox</pre>

可以看到push失败，具体错误如下：

<pre>
2015/01/05 11:01:17 Error: Invalid registry endpoint https://192.168.112.136:5000/v1/: Get https://192.168.112.136:5000/v1/_ping: dial tcp 192.168.112.136:5000: connection refused. If this private registry supports only HTTP or HTTPS with an unknown CA certificate, please add `--insecure-registry 192.168.112.136:5000` to the daemon's arguments. In the case of HTTPS, if you have access to the registry's CA certificate, no need for the flag; simply place the CA certificate at /etc/docker/certs.d/192.168.112.136:5000/ca.crt
</pre>

因为Docker从1.3.X之后，与docker registry交互默认使用的是https，然而此处搭建的私有仓库只提供http服务，所以当与私有仓库交互时就会报上面的错误。为了解决这个问题需要在启动docker server时增加启动参数为默认使用http访问。修改docker启动配置文件（此处是修改10.211.55.30机器的配置）centos7下配置文件地址为：/usr/lib/systemd/system/docker.service，在其中增加–insecure-registry 10.211.55.30:5000如下所示：

<pre>
[Unit]
 Description=Docker Application Container Engine
 Documentation=http://docs.docker.com
 After=network.target rhel-push-plugin.socket
 Wants=docker-storage-setup.service

 [Service]
 Type=notify
 NotifyAccess=all
 EnvironmentFile=-/etc/sysconfig/docker
 EnvironmentFile=-/etc/sysconfig/docker-storage
 EnvironmentFile=-/etc/sysconfig/docker-network
 Environment=GOTRACEBACK=crash
 ExecStart=/usr/bin/docker-current daemon \
  --exec-opt native.cgroupdriver=systemd \
  --insecure-registry=10.211.55.30:5000 \
  $OPTIONS \
  $DOCKER_STORAGE_OPTIONS \
  $DOCKER_NETWORK_OPTIONS \
  $ADD_REGISTRY \
  $BLOCK_REGISTRY \
  $INSECURE_REGISTRY
 LimitNOFILE=1048576
 LimitNPROC=1048576
 LimitCORE=infinity
 TimeoutStartSec=0
 MountFlags=slave
 Restart=on-abnormal

 [Install]
 WantedBy=multi-user.target
 </pre>

修改完之后，重启Docker服务。

<pre>$ restart docker</pre>

重启完之后我们再次运行推送命令，把本地镜像推送到私有服务器上。

<pre>$ sudo docker push 10.211.55.30:5000/busybox</pre>

可以看到镜像已经push到私有仓库中去了。

进行到这一步的时候也不一定能够成功，使用journalctl -f 可以查看日志信息，通过日志信息可以查看到如下信息，关注标红的部分，显示的是SELinux 的问题，我的处理方法是直接关闭SELinux

<pre>
Jan 27 16:08:16 server01 docker-current[15241]: time="2017-01-27T08:08:16Z" level=error msg="response completed with error" err.code="blob unknown" err.detail=sha256:45a2e645736c4c66ef34acce2407ded21f7a9b231199d3b92d6c9776df264729 err.message="blob unknown to registry" go.version=go1.7.3 http.request.host="10.211.55.30:5000" http.request.id=a2dbff10-2937-4e9e-94f3-16275739ad61 http.request.method=HEAD http.request.remoteaddr="10.211.55.30:48256" http.request.uri="/v2/centos_20170127/blobs/sha256:45a2e645736c4c66ef34acce2407ded21f7a9b231199d3b92d6c9776df264729" http.request.useragent="docker/1.10.3 go/go1.6.3 git-commit/cb079f6-unsupported kernel/3.10.0-327.36.3.el7.x86_64 os/linux arch/amd64" http.response.contenttype="application/json; charset=utf-8" http.response.duration=1.4262ms http.response.status=404 http.response.written=157 instance.id=f9f97de9-15bc-41e3-9ec3-f1033e57a77e vars.digest="sha256:45a2e645736c4c66ef34acce2407ded21f7a9b231199d3b92d6c9776df264729" vars.name="centos_20170127" version=v2.6.0
 Jan 27 16:08:16 server01 docker-current[15241]: 10.211.55.30 - - [27/Jan/2017:08:08:16 +0000] "HEAD /v2/centos_20170127/blobs/sha256:45a2e645736c4c66ef34acce2407ded21f7a9b231199d3b92d6c9776df264729 HTTP/1.1" 404 157 "" "docker/1.10.3 go/go1.6.3 git-commit/cb079f6-unsupported kernel/3.10.0-327.36.3.el7.x86_64 os/linux arch/amd64"
 Jan 27 16:08:16 server01 docker-current[15241]: time="2017-01-27T08:08:16Z" level=error msg="response completed with error" err.code=unknown err.detail="filesystem: mkdir /var/lib/registry/docker: permission denied" err.message="unknown error" go.version=go1.7.3 http.request.host="10.211.55.30:5000" http.request.id=158612a0-39f5-41f5-9985-c80db7da911f http.request.method=POST http.request.remoteaddr="10.211.55.30:48258" http.request.uri="/v2/centos_20170127/blobs/uploads/" http.request.useragent="docker/1.10.3 go/go1.6.3 git-commit/cb079f6-unsupported kernel/3.10.0-327.36.3.el7.x86_64 os/linux arch/amd64" http.response.contenttype="application/json; charset=utf-8" http.response.duration=1.671997ms http.response.status=500 http.response.written=164 instance.id=f9f97de9-15bc-41e3-9ec3-f1033e57a77e vars.name="centos_20170127" version=v2.6.0
 Jan 27 16:08:16 server01 docker-current[15241]: 10.211.55.30 - - [27/Jan/2017:08:08:16 +0000] "POST /v2/centos_20170127/blobs/uploads/ HTTP/1.1" 500 164 "" "docker/1.10.3 go/go1.6.3 git-commit/cb079f6-unsupported kernel/3.10.0-327.36.3.el7.x86_64 os/linux arch/amd64"
 Jan 27 16:08:16 server01 docker-current[15241]: time="2017-01-27T16:08:16.115997771+08:00" level=error msg="Upload failed, retrying: Received unexpected HTTP status: 500 Internal Server Error"
 Jan 27 16:08:16 server01 setroubleshoot[5771]: failed to retrieve rpm info for /opt/data/registry
 Jan 27 16:08:16 server01 setroubleshoot[5771]: SELinux is preventing /bin/registry from write access on the directory /opt/data/registry. For complete SELinux messages. run sealert -l 748743d8-dd8a-4482-9771-94a403bccf18
 Jan 27 16:08:16 server01 python[5771]: SELinux is preventing /bin/registry from write access on the directory /opt/data/registry.

  ***** Plugin catchall_labels (83.8 confidence) suggests *******************

  If you want to allow registry to have write access on the registry directory
  Then you need to change the label on /opt/data/registry
  Do
  # semanage fcontext -a -t FILE_TYPE '/opt/data/registry'
  where FILE_TYPE is one of the following: cgroup_t, docker_var_lib_t, svirt_home_t, svirt_sandbox_file_t, virt_home_t.
  Then execute:
  restorecon -v '/opt/data/registry'


  ***** Plugin catchall (17.1 confidence) suggests **************************

  If you believe that registry should be allowed write access on the registry directory by default.
  Then you should report this as a bug.
  You can generate a local policy module to allow this access.
  Do
  allow this access for now by executing:
  # grep registry /var/log/audit/audit.log | audit2allow -M mypol
  # semodule -i mypol.pp
  </pre>

关闭方法如下
<pre>
查看SELinux状态：
1. /usr/sbin/sestatus -v ##如果SELinux status参数为enabled即为开启状态
 SELinux status: enabled
2. getenforce ##也可以用这个命令检查
关闭SELinux：
1. 临时关闭（不用重启机器）：
 setenforce 0 ##设置SELinux 成为permissive模式
 ##setenforce 1 设置SELinux 成为enforcing模式
2. 修改配置文件需要重启机器：
 修改/etc/selinux/config 文件
 将SELINUX=enforcing改为SELINUX=disabled
 重启机器即可
</pre>

接下来我们删除本地镜像，然后从私有仓库中pull下来该镜像。

<pre>$ sudo docker pull 10.211.55.30:5000/busybox</pre>

到此就搭建好了Docker私有仓库。上面搭建的仓库是不需要认证的，我们可以结合nginx和https实现认证和加密功能。

### 4. 管理仓库中的镜像

**查询**

在Private Registry2中查看或检索Repository或images， 将不能用docker search，会报下边的错误

<pre>$ docker search 10.211.55.30:5000/busybox/
 Error response from daemon: Unexpected status code 404</pre>

但通过v2版本的API，我们可以实现相同目的，必须按照IP:port/v2/_catalog格式：

<pre>
[root@server01 ~]# curl http://10.211.55.30:5000/v2/_catalog
 {"repositories":["centos"]}
 [root@server01 ~]# curl http://10.211.55.30:5000/v2/centos/tags/list
 {"name":"centos","tags":["latest"]}
</pre>

拉取镜像如下
<pre>
[root@server01 ~]# docker pull 10.211.55.30:5000/centos
 Using default tag: latest
 Trying to pull repository 10.211.55.30:5000/centos ...
 latest: Pulling from 10.211.55.30:5000/centos
 Digest: sha256:7dfffa13a2addc317ac3bdfbddbd4604ea629decea19c271481e5c45245b7612
 Status: Downloaded newer image for 10.211.55.30:5000/centos:latest
</pre>

**删除**

目前尚未找到方法删除私有仓库中的镜像，尝试过直接从仓库存储目录中删除镜像文件，但是并不能成功删除镜像。

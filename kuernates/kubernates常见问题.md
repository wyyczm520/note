# kubernetes 常见问题

**1) Failed to get api versions from server: Get http://10.211.55.20:8080/api: dial tcp 10.211.55.20:8080: getsockopt: connection refused**

解决方案：kube-controller-manager的配置文件/etc/kubernetes/controller-manager中也需要设置KUBE_MASTER="http://10.211.55.20:8080"

**2) 创建Nginx Pod过程中报如下错误：**

#kubectlcreate -f nginx-pod.yaml
Error from server: error when creating "nginx-pod.yaml": Pod "nginx" is forbidden: no API token found for service account default/default, retry after the token is automatically created and added to the service account
解决方法：
1> 修改/etc/kubernetes/apiserver文件中KUBE_ADMISSION_CONTROL参数。
修改前：
KUBE_ADMISSION_CONTROL="--admission_control=NamespaceLifecycle,NamespaceExists,LimitRanger,SecurityContextDeny,ServiceAccount,ResourceQuota"
去掉“ServiceAccount”选项。
2> 重启kube-apiserver服务
# systemctl restart kube-apiserver。


**3) 创建Pod过程中，显示异常，通过查看日志/var/log/messages，有以下报错信息：
Nov 26 21:52:06 localhost kube-apiserver: E1126 21:52:06.697708    1963 server.go:454] Unable to generate self signed cert: open /var/run/kubernetes/apiserver.crt: permission denied
Nov 26 21:52:06 localhost kube-apiserver: E1126 21:52:06.697818    1963 server.go:464] Unable to listen for secure (open /var/run/kubernetes/apiserver.crt: no such file or directory); will try again.**

解决方法有两种：

第一种方法：

```
# vim /usr/lib/systemd/system/kube-apiserver.service
[Service]
PermissionsStartOnly=true
ExecStartPre=-/usr/bin/mkdir /var/run/kubernetes
ExecStartPre=/usr/bin/chown -R kube:kube /var/run/kubernetes/
# systemctl daemon-reload
# systemctl restart kube-apiserver
```

第二种方法：

```
# vim /etc/kubernetes/apiserver
KUBE_API_ARGS="--secure-port=0"
在KUBE_API_ARGS加上--secure-port=0参数。
原因如下：
--secure-port=6443: The port on which to serve HTTPS with authentication and authorization. If 0, don't serve HTTPS at all.
```


**4) 在利用yaml文件创建Pod过程中，报“Error from server：the server could not find the requested resource”**

这个问题最坑，原因是Kubernetes版本太低，虽然node节点的状态显示是Ready，但无法创建Pod。
伴随现象：在描述节点状态时，显示如下，正常的是没有红色方框部分。

而且显示的版本信息如下：
注意：该版本信息却与Kubernetes官方提供的yum源中的版本信息吻合：http://cbs.centos.org/repos/virt7-testing/x86_64/os/Packages/

而最新的版本信息为：

Kubelet Version: v1.0.3.34+b9a88a7d0e357b

问题原因：

用了CentOS自带光盘作为本地yum源，而删除了CentOS自带的网络yum源的配置文件
解决方法：

使用CentOS自带的网络yum源的配置文件。



**5) mapping values are not allowed here
     出现这个错误的是因为yaml格式不正确,谷歌定义的yaml格式太严格了,每个冒号后面都必须带有空格**

app.yaml
handlers:
-空格url: /.*
空格空格script: hello.py    // 下面必须有两个空格,对齐的也太夸张了

**6) the project is not exist(app_id=u)**

   app.yaml的application名称一定要与在 https://appengine.google.com/ 建立的应用程序名称一致

**7) [root@server01 cluster_dns]# kubectl --server=10.211.55.20:8080 create -f influxdb-service.yaml
Error from server: error when creating "influxdb-service.yaml": Internal error occurred: failed to allocate a serviceIP: range is full**

是因为/etc/kubernetes/apiserver配置不对


**8) Failed to retrieve network config: client: etcd cluster is unavailable or misconfigured**

解决方案：可能是flanneld重配置的ETC的目标地址不对

**9) Unable to write event: 'dial tcp 10.211.55.30:8080: getsockopt: connection refused' (may retry after sleeping)**

解决方案：
vi /etc/kubernetes/apiserver
注释掉KUBE_API_ADDRESS="--insecure-bind-address=127.0.0.1"
添加KUBE_API_ADDRESS="--address=0.0.0.0"
删除KUBE_ADMISSION_CONTROL默认包含的ServiceAccount


**10) kube-controller-manager报cannot be updated: the object has been modified; please apply your changes to the latest version and**

解决方案

是因为kube-controller-manager配置不正确导致的


**11.WARNING IPv4 forwarding is disabled. Networking will not work
创建swarm cluster的时候报错：
WARNING: IPv4 forwarding is disabled. Networking will not work.
2016/05/20 09:15:35 Post https://discovery.hub.docker.com/v1/clusters: dial tcp: lookup discovery.hub.docker.com on 192.168.10.2:53: read udp 172.17.0.2:56087->192.168.10.2:53: i/o timeout**

解决办法：

```
# vi /etc/sysctl.conf
或者
# vi /usr/lib/sysctl.d/00-system.conf
添加如下代码：
    net.ipv4.ip_forward=1

重启network服务
# systemctl restart network
查看是否修改成功
# sysctl net.ipv4.ip_forward
如果返回为“net.ipv4.ip_forward = 1”则表示成功了
```

确保系统内核是最新的

```
$ uname -r
3.10.0-229.el7.x86_64
```

使用yum安装

##### 1.使用有root权限的用户登录

##### 2.确保目前机器上的yum是最新的

```
$ sudo yum update
```

##### 3.添加docker yum源

```
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF' [dockerrepo] name=Docker Repository baseurl=https://yum.dockerproject.org/repo/main/centos/7/ enabled=1 gpgcheck=1 gpgkey=https://yum.dockerproject.org/gpg EOF
```

##### 4.安装Docker package

```
$ sudo yum install docker-engine
Loaded plugins: fastestmirror, langpacks
dockerrepo                                                    | 2.9 kB  00:00:00     
dockerrepo/primary_db                                         |  20 kB  00:00:01     
Loading mirror speeds from cached hostfile
 * base: mirrors.btte.net
 * extras: mirrors.cug.edu.cn
 * updates: mirrors.cug.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package docker-engine.x86_64 0:1.12.1-1.el7.centos will be installed
--> Processing Dependency: docker-engine-selinux >= 1.12.1-1.el7.centos for package: docker-engine-1.12.1-1.el7.centos.x86_64
--> Running transaction check
---> Package docker-engine-selinux.noarch 0:1.12.1-1.el7.centos will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=====================================================================================
 Package                   Arch       Version                   Repository      Size
=====================================================================================
Installing:
 docker-engine             x86_64     1.12.1-1.el7.centos       dockerrepo      19 M
Installing for dependencies:
 docker-engine-selinux     noarch     1.12.1-1.el7.centos       dockerrepo      28 k

Transaction Summary
=====================================================================================
Install  1 Package (+1 Dependent package)

Total download size: 19 M
Installed size: 79 M
Is this ok [y/d/N]: y
Downloading packages:
warning: /var/cache/yum/x86_64/7/dockerrepo/packages/docker-engine-selinux-1.12.1-1.el7.centos.noarch.rpm: Header V4 RSA/SHA512 Signature, key ID 2c52609d: NOKEY
Public key for docker-engine-selinux-1.12.1-1.el7.centos.noarch.rpm is not installed
(1/2): docker-engine-selinux-1.12.1-1.el7.centos.noarch.rpm   |  28 kB  00:00:01     
(2/2): docker-engine-1.12.1-1.el7.centos.x86_64.rpm                                                                                    |  19 MB  00:08:26     
--------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                          38 kB/s |  19 MB  00:08:26     
Retrieving key from https://yum.dockerproject.org/gpg
Importing GPG key 0x2C52609D:
 Userid     : "Docker Release Tool (releasedocker) <docker@docker.com>"
 Fingerprint: 5811 8e89 f3a9 1289 7c07 0adb f762 2157 2c52 609d
 From       : https://yum.dockerproject.org/gpg
Is this ok [y/N]: y
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : docker-engine-selinux-1.12.1-1.el7.centos.noarch                                                                                           1/2
restorecon:  lstat(/var/lib/docker) failed:  No such file or directory
warning: %post(docker-engine-selinux-1.12.1-1.el7.centos.noarch) scriptlet failed, exit status 255
Non-fatal POSTIN scriptlet failure in rpm package docker-engine-selinux-1.12.1-1.el7.centos.noarch
  Installing : docker-engine-1.12.1-1.el7.centos.x86_64                                                                                                   2/2
  Verifying  : docker-engine-selinux-1.12.1-1.el7.centos.noarch                                                                                           1/2
  Verifying  : docker-engine-1.12.1-1.el7.centos.x86_64                                                                                                   2/2

Installed:
  docker-engine.x86_64 0:1.12.1-1.el7.centos                                                                                                                  

Dependency Installed:
  docker-engine-selinux.noarch 0:1.12.1-1.el7.centos                                                                                                          

Complete!
```

##### 5.查看安装版本

```
# docker -v
Docker version 1.12.1, build 23cf638
```

##### 6启动docker damon

```
# sudo service docker start

Redirecting to /bin/systemctl start  docker.service
```

##### 7.通过运行一个容器的镜像来检查docker是否安装成功

```
# sudo docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c04b14da8d14: Pull complete
Digest: sha256:0256e8a36e2070f7bf2d0b0763dbabdd67798512411de4cdcf9431a1feb60fd9
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
 ```

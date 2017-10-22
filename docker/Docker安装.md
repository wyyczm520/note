#### 安装前置条件

Red hat 6以上，并且是64位CPU

##### 1.检查内核

uname -a ：显示系统名、节点名称、操作系统的发行版号、操作系统版本、运行系统的机器 ID 号。

##### 2.检查Device Mapper

我们使用Device Mapper作为Docker的存储驱动，为Docker提供存储能力，在Red Hat企业版Linux以及更高的版本的宿主机中，应该都安装了Device Mapper，不过仍然需要确认一下。

```
ls -l /sys/class/misc/device-mapper
```

同样，也可以在/proc/devices文件中检查是否有device-mapper条目

```
sudo grep device-mapper /proc/devices
```


#### 安装

##### 1.在RHEL 7中安装docker

安装下载的安装包

```
sudo rpm -ivh ***.rpm
```

效果如下

```
Retrieving http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
warning: /var/tmp/rpm-tmp.UOfa9g: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY
Preparing...                ########################################### [100%]
   1:epel-release           ########################################### [100%]
```

##### 安装

```
sudo yum -y install docker-io
```

效果如下

```
[root@server01 Desktop]# sudo yum -y install docker-io
Loaded plugins: product-id, refresh-packagekit, security, subscription-manager
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
epel/metalink                                            | 4.9 kB     00:00     
https://free.nchc.org.tw/fedora-epel/6/x86_64/repodata/repomd.xml: [Errno 14] Peer cert cannot be verified or peer cert invalid
Trying other mirror.
epel                                                     | 4.3 kB     00:01     
epel/primary_db                                          | 5.9 MB     03:23     
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package docker-io.x86_64 0:1.7.1-2.el6 will be installed
--> Processing Dependency: lxc for package: docker-io-1.7.1-2.el6.x86_64
--> Running transaction check
---> Package lxc.x86_64 0:1.0.8-1.el6 will be installed
--> Processing Dependency: lua-lxc(x86-64) = 1.0.8-1.el6 for package: lxc-1.0.8-1.el6.x86_64
--> Processing Dependency: lua-alt-getopt for package: lxc-1.0.8-1.el6.x86_64
--> Processing Dependency: liblxc.so.1()(64bit) for package: lxc-1.0.8-1.el6.x86_64
--> Running transaction check
---> Package lua-alt-getopt.noarch 0:0.7.0-1.el6 will be installed
---> Package lua-lxc.x86_64 0:1.0.8-1.el6 will be installed
--> Processing Dependency: lua-filesystem for package: lua-lxc-1.0.8-1.el6.x86_64
---> Package lxc-libs.x86_64 0:1.0.8-1.el6 will be installed
--> Running transaction check
---> Package lua-filesystem.x86_64 0:1.4.2-1.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                Arch           Version               Repository    Size
================================================================================
Installing:
 docker-io              x86_64         1.7.1-2.el6           epel         4.6 M
Installing for dependencies:
 lua-alt-getopt         noarch         0.7.0-1.el6           epel         6.9 k
 lua-filesystem         x86_64         1.4.2-1.el6           epel          24 k
 lua-lxc                x86_64         1.0.8-1.el6           epel          16 k
 lxc                    x86_64         1.0.8-1.el6           epel         122 k
 lxc-libs               x86_64         1.0.8-1.el6           epel         255 k

Transaction Summary
================================================================================
Install       6 Package(s)

Total download size: 5.0 M
Installed size: 20 M
Downloading Packages:
(1/6): docker-io-1.7.1-2.el6.x86_64.rpm                                                                                                                   | 4.6 MB     01:57     
(2/6): lua-alt-getopt-0.7.0-1.el6.noarch.rpm                                                                                                              | 6.9 kB     00:00     
(3/6): lua-filesystem-1.4.2-1.el6.x86_64.rpm                                                                                                              |  24 kB     00:00     
(4/6): lua-lxc-1.0.8-1.el6.x86_64.rpm                                                                                                                     |  16 kB     00:00     
(5/6): lxc-1.0.8-1.el6.x86_64.rpm                                                                                                                         | 122 kB     00:03     
(6/6): lxc-libs-1.0.8-1.el6.x86_64.rpm                                                                                                                    | 255 kB     00:05     
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                             39 kB/s | 5.0 MB     02:09     
warning: rpmts_HdrFromFdno: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
Importing GPG key 0x0608B895:
 Userid : EPEL (6) <epel@fedoraproject.org>
 Package: epel-release-6-8.noarch (installed)
 From   : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
Warning: RPMDB altered outside of yum.
  Installing : lxc-libs-1.0.8-1.el6.x86_64                                                                                                                                   1/6
  Installing : lua-filesystem-1.4.2-1.el6.x86_64                                                                                                                             2/6
  Installing : lua-lxc-1.0.8-1.el6.x86_64                                                                                                                                    3/6
  Installing : lua-alt-getopt-0.7.0-1.el6.noarch                                                                                                                             4/6
  Installing : lxc-1.0.8-1.el6.x86_64                                                                                                                                        5/6
  Installing : docker-io-1.7.1-2.el6.x86_64                                                                                                                                  6/6
  Verifying  : lxc-libs-1.0.8-1.el6.x86_64                                                                                                                                   1/6
  Verifying  : lua-lxc-1.0.8-1.el6.x86_64                                                                                                                                    2/6
  Verifying  : lxc-1.0.8-1.el6.x86_64                                                                                                                                        3/6
  Verifying  : docker-io-1.7.1-2.el6.x86_64                                                                                                                                  4/6
  Verifying  : lua-alt-getopt-0.7.0-1.el6.noarch                                                                                                                             5/6
  Verifying  : lua-filesystem-1.4.2-1.el6.x86_64                                                                                                                             6/6

Installed:
  docker-io.x86_64 0:1.7.1-2.el6                                                                                                                                                 

Dependency Installed:
  lua-alt-getopt.noarch 0:0.7.0-1.el6     lua-filesystem.x86_64 0:1.4.2-1.el6     lua-lxc.x86_64 0:1.0.8-1.el6     lxc.x86_64 0:1.0.8-1.el6     lxc-libs.x86_64 0:1.0.8-1.el6    

Complete!
```


##### 查看版本

```
docker -v
```

#### 在Red Hat6系发行版中启动Docker守护进程

##### 启动守护进程

```
sudo service docker start
```

##### 想要在系统开机时自动启动Docker服务，还要执行如下命令

```
sudo service docker enable
```

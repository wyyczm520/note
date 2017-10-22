
#### 查看centos linux 后台日志    

```
journalctl -f
```

##### bash:ifconfig command not found

```
export PATH="$PATH:/sbin"
```

##### 重置bash

```
source /etc/profile
```

##### bash: ifconfig: command not found

```
/sbin/ifconfig
```
##### 查看CPU核数

```
cat /proc/cpuinfo
```
/proc 文件夹下存放的是系统相关信息

##### 硬盘大小：

```
df -lh
```

##### 打开和查看端口是否开启

```
nc -lp 23 & （即telnet）
netstat -an|grep 23 （查看端口是否正常打开
```

##### tomcat视图用户配置

```
<role rolename="tomcat"/>
<role rolename="manager"/>
<role rolename="manager-gui"/>
<user username="tomcat" password="tomcat" roles="tomcat,manager"/>
<user username="both" password="tomcat" roles="tomcat,role1"/>
<user username="role1" password="tomcat" roles="role1"/>
<user username="admin" password="admin" roles="manager-gui"/>
```

##### 重启

```
1、shutdown -r now  
2、poweroff
3、init
4、reboot
5、halt
```

##### 更改文件用户和用户组

```
chown hadoop /tmp
chgrp  hadoop /tmp
或者同时改变
chown hadoop:hadoop /tmp
```

##### 查看是否安装程序

```
rpm  -qa | grep  mysql
```

##### 按照文件大小排列文件

```
du -k *EBIZ_TO_100_20140705* |sort -rn |more
```

##### 查找文件中的指定字符

```
find <directory> -type f -name "*.c" | xargs grep "<strings>"
```

##### 批量下载文件

```
wget ftp://10.4.191.238:10021/xyyx --ftp-user=ec --ftp-password=EcFtp,1126 -r
```

##### 统计字符个数

```
grep -wc string file
```

##### 查找大文件

```
du -k . |sort -nr |more
```

##### 升级内核

```
uname -r
```

##### 查询文件

```
cat  access_log.2016-08-27.log |grep "2016:12:35" | wc -l
cat access_log.2016-08-27.log | grep '2016:12:35' | grep -E '.js|.png|.jpg|.css' | wc -l
```

##### 指定安装路径

不指定prefix，则可执行文件默认放在/usr /local/bin，库文件默认放在/usr/local/lib，配置文件默认放在/usr/local/etc。其它的资源文件放在/usr /local/share。你要卸载这个程序，要么在原来的make目录下用一次make uninstall（前提是make文件指定过uninstall）,要么去上述目录里面把相关的文件一个个手工删掉。
指定prefix，直接删掉一个文件夹就够了。

```
configure --prefix的含义
```

##### 查询被占用端口

```
netstat -tunlp |grep 端口
```

##### 解压缩

```

.tar.gz     格式解压为          tar   -zxvf   xx.tar.gz
.tar.bz2   格式解压为          tar   -jxvf    xx.tar.bz2
```


##### 查看守护进程

```
ps axj
```

##### SCP命令

```
scp curator.tar root@10.4.46.214:/data/elk-images/2
```

#### 查看端口占用

```
lsof -i:9191 | grep -i listen
```

### apache运维命令

```
查看了线程中的 httpd 的数量
ps -aux | grep httpd
ps -aux | grep httpd | wc -l 
查看了连接数和当前的连接数
netstat -ant | grep $ip:80 | wc -l 
netstat -ant | grep $ip:80 | grep EST | wc -l
```

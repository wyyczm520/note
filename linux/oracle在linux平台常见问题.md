### 错误一：cannot restore segment prot after reloc: Permission denied 解决方法

```warningssecurityfilegoogleserver``` 编辑```/etc/selinux/config```，找到这段：

```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
# enforcing - SELinux security policy is enforced.
# permissive - SELinux prints warnings instead of enforcing.
# disabled - SELinux is fully disabled.
SELINUX=enforcing
```

把 ```SELINUX=enforcing``` 注释掉：```#SELINUX=enforcing``` ，然后新加一行为：

```
SELINUX=disabled
```

保存，关闭。

编辑```/etc/sysconfig/selinux```，找到:

```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
# enforcing - SELinux security policy is enforced.
# permissive - SELinux prints warnings instead of enforcing.
# disabled - SELinux is fully disabled.
SELINUX=enforcing
```

如果SELINUX已经是 ```SELINUX=disabled```，那么就不用改了，否则就把```SELINUX=enforcing``` 注释掉，新加一行：

```
SELINUX=disabled
```

保存，退出。

如果你碰到其他类似提示：
```cannot restore segment prot after reloc: Permission denied```

哪应该是SELinux的问题，可以考虑把它关闭。

郁闷的是.我把SELinux关闭后还是不行.于是到google上search.发现这个很有用.

在你保证SElinux 被disable后.还执行下

```
chcon -t texrel_shlib_t
```

如:

```
chcon -t texrel_shlib_t /路径/路径/名字.so (这个文件视具体执行文件.)

```

以上两步.已经解决了很多server的问题了.

### 错误二 invalid specification for system parameter LOCAL_LISTENER错误

oracle 11g 报 ```ORA-00119: invalid specification for system parameter LOCAL_LISTENER```错误
 
在linux下启动oracle11g是报如下错误：

```
ORA-00119: invalid specification for system parameter LOCAL_LISTENER
ORA-00130: invalid listener address '(ADDRESS=(PROTOCOL=TCP)(HOST=oracle11g)(PORT=1521))'
```

当初怀疑改机器名的原因引起的。查了一下机器名没有错误。

```
# hostname
oracle11g
```

检查```/etc/hosts```里的条目。只有一个

```
127.0.0.1 localhost.localdomain localhost
```

于是在后面加了一项

```
172.1.1.10 oracle11g
```

之后启动数据库成功

```
SQL> startup
ORACLE instance started.

Total System Global Area 417546240 bytes
Fixed Size 2213936 bytes
Variable Size 276826064 bytes
Database Buffers 134217728 bytes
Redo Buffers 4288512 bytes
Database mounted.
Database opened.
```

我装了一个11g的库，在配置了LOCAL_LISTENER之后，关闭数据库，再次开启的时候，报该错误，按照上面的办法，解决之；但是有个疑问，作为系统级参数，为什么使用show parameter 产看的时候，显示为空，但是，开机的时候，却报这样的错误?

#### 启动

##### 一、在Linux下启动Oracle

登录到CentOS，切换到oracle用户权限

```
# su – oracle
```

接着输入：

```
$ sqlplus "/as sysdba"
```

原本的画面会变为

```
SQL>
```

接着请输入

```
SQL> startup
```
就可以正常的启动数据库了。

如果没有启动，可以输入：

```
$ lsnrctl start
```

启动监听器

```
SQL> conn sys@orcl as sysdba
```

然后输入密码，sys以sysdba身份登入数据库。

##### 三、启动emctl

另外也可以发现```http://localhost.localdomain:1158/em``` 目前是没有反应的，这边要另外启动，启动的指令如下：

```
$ emctl start dbconsole
```

这个指令运行时间较长，执行完的画面如下：

手动启动Oracle数据库完毕，下面创建系统自行启动Oracle的脚本。

##### 四、Oracle启动&停止脚本

1.修改Oracle系统配置文件：/etc/oratab，只有这样，Oracle 自带的dbstart和dbshut才能够发挥作用。

```
# vi /etc/oratab
orcl:/opt/oracle/102:Y
# Entries are of the form:
#   $ORACLE_SID:$ORACLE_HOME:<N|Y>:
```

2.在 /etc/init.d/ 下创建文件oracle，内容如下：

```
#!/bin/sh
# chkconfig: 35 80 10
# description: Oracle auto start-stop script.
#
# Set ORA_HOME to be equivalent to the $ORACLE_HOME
# from which you wish to execute dbstart and dbshut;
#
# Set ORA_OWNER to the user id of the owner of the
# Oracle database in ORA_HOME.
ORA_HOME=/opt/oracle/102
ORA_OWNER=oracle
if [ ! -f $ORA_HOME/bin/dbstart ]
then
    echo "Oracle startup: cannot start"
    exit
fi
case "$1" in
'start')
# Start the Oracle databases:
echo "Starting Oracle Databases ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Starting Oracle Databases as part of system up." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart" >>/var/log/oracle
echo "Done"
# Start the Listener:
echo "Starting Oracle Listeners ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Starting Oracle Listeners as part of system up." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/lsnrctl start" >>/var/log/oracle
echo "Done."
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Finished." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
touch /var/lock/subsys/oracle
;;
'stop')
# Stop the Oracle Listener:
echo "Stoping Oracle Listeners ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Stoping Oracle Listener as part of system down." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/lsnrctl stop" >>/var/log/oracle
echo "Done."
rm -f /var/lock/subsys/oracle
# Stop the Oracle Database:
echo "Stoping Oracle Databases ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Stoping Oracle Databases as part of system down." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut" >>/var/log/oracle
echo "Done."
echo ""
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Finished." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
;;
'restart')
$0 stop
$0 start
;;
esac
```

3.改变文件权限

```
# chmod 755 /etc/init.d/oracle
```

4.添加服务

```
# chkconfig --level 35 oracle on
```

5.需要在关机或重启机器之前停止数据库，做一下操作

```
# ln -s /etc/init.d/oracle /etc/rc0.d/K01oracle   //关机
# ln -s /etc/init.d/oracle /etc/rc6.d/K01oracle   //重启 
```

6.使用方法

```
# service oracle start        //启动oracle
# service oracle stop        //关闭oracle
# service oracle restart     //重启oracle
```

7.测试

**a. 开机自启动**

```
Last login: Mon Nov 26 19:57:06 2012 from 10.0.0.145
[root@ORS ~]# su - oracle
[oracle@ORS ~]$ sqlplus "/as sysdba"

SQL*Plus: Release 10.2.0.1.0 - Production on Mon Nov 26 20:07:33 2012

Copyright (c) 1982, 2005, Oracle.  All rights reserved.


Connected to:
Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - Production
With the Partitioning, OLAP and Data Mining options

SQL> set linesize 300;
SQL> set pagesize 30;
SQL> select * from scott.emp;

     EMPNO ENAME      JOB              MGR HIREDATE         SAL       COMM     DEPTNO
---------- ---------- --------- ---------- --------- ---------- ---------- ----------
      7369 SMITH      CLERK           7902 17-DEC-80        800                    20
      7499 ALLEN      SALESMAN        7698 20-FEB-81       1600        300         30
      7521 WARD       SALESMAN        7698 22-FEB-81       1250        500         30
      7566 JONES      MANAGER         7839 02-APR-81       2975                    20
      7654 MARTIN     SALESMAN        7698 28-SEP-81       1250       1400         30
      7698 BLAKE      MANAGER         7839 01-MAY-81       2850                    30
      7782 CLARK      MANAGER         7839 09-JUN-81       2450                    10
      7788 SCOTT      ANALYST         7566 19-APR-87       3000                    20
      7839 KING       PRESIDENT            17-NOV-81       5000                    10
      7844 TURNER     SALESMAN        7698 08-SEP-81       1500          0         30
      7876 ADAMS      CLERK           7788 23-MAY-87       1100                    20
      7900 JAMES      CLERK           7698 03-DEC-81        950                    30
      7902 FORD       ANALYST         7566 03-DEC-81       3000                    20
      7934 MILLER     CLERK           7782 23-JAN-82       1300                    10

14 rows selected.

SQL>


b. service oracle stop
SQL> Disconnected from Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - Production
With the Partitioning, OLAP and Data Mining options
[oracle@ORS ~]$ logout
[root@ORS ~]# service oracle stop
Stoping Oracle Listeners ...
Done.
Stoping Oracle Databases ...
Done.
[root@ORS ~]# su - oracle
[oracle@ORS ~]$ sqlplus "/as sysdba"
SQL*Plus: Release 10.2.0.1.0 - Production on Mon Nov 26 20:17:20 2012
Copyright (c) 1982, 2005, Oracle.  All rights reserved.
Connected to an idle instance.
SQL> set linesize 300;
SQL> set pagesize 30;
SQL> select * from scott.emp;
select * from scott.emp
*
ERROR at line 1:
ORA-01034: ORACLE not available
SQL>



c. service oracle start
SQL> Disconnected
[oracle@ORS ~]$ logout
[root@ORS ~]# service oracle start
Starting Oracle Databases ...
Done
Starting Oracle Listeners ...
Done.
[root@ORS ~]#

d. service oracle restart
[root@ORS ~]# service oracle restart
Stoping Oracle Listeners ...
Done.
Stoping Oracle Databases ...
Done.
Starting Oracle Databases ...
Done
Starting Oracle Listeners ...
Done.
[root@ORS ~]#
```


至此，Oracle服务启动&停止脚本与开机自启动设置完毕。

```
CentOS 6.3(x32)下安装Oracle 10g R2
http://www.cnblogs.com/mchina/archive/2012/11/06/2737472.html


ORA-00845: MEMORY_TARGET not supported on this system
```

今天晚上新装一台Oracle 11g的数据库，打算将SGA设大一点，知道 11g 中有一个新特新 MEMORY_TARGET，于是尝一下鲜，谁知报了个 ORA-00845，报错比较容易迷惑人，不借助Google真得想半天：

```
	SQL> alter system set memory_max_target=3G scope=spfile ;
	System altered.
	SQL> alter system set memory_target=2G scope=spfile ;      
	System altered.
	SQL>
	SQL> shutdown immediate
	Database closed.
	Database dismounted.
	ORACLE instance shut down.
	SQL> startup ;
	ORA-00845: MEMORY_TARGET not supported on this system
```

来自Oracle的官方解析是：

```
Starting with Oracle Database 11g, the Automatic Memory Management feature requires more shared memory (/dev/shm)and file descriptors. The size of the shared memory should be at least the greater of MEMORY_MAX_TARGET and MEMORY_TARGET for each Oracle instance on the computer. If MEMORY_MAX_TARGET or MEMORY_TARGET is set to a non zero value, and an incorrect size is assigned to the shared memory, it will result in an ORA-00845 error at startup.
```

简单来说就是 MEMORY_MAX_TARGET 的设置不能超过 ```/dev/shm``` 的大小：

```
1	[oracle@FWDB FWDB]$ df -h | grep shm
2	tmpfs                 2.0G     0  2.0G   0% /dev/shm
```

还真是撞到这个枪口上了：

马上把它加大：

```
1	[root@FWDB ~]# cat /etc/fstab | grep tmpfs
2	tmpfs                   /dev/shm                tmpfs   defaults,size=4G 0 0
```

现在可以通过重启使这个配置生效，也可以通过重新挂载来修改其大小：

```
1	[root@FWDB ~]# mount -o remount,size=4G /dev/shm
2	[root@FWDB ~]# df -h | grep shm
3	tmpfs                 4.0G     0  4.0G   0% /dev/shm
```

再次启动数据库，没有报错了。

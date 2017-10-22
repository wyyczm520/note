## MongoDB安装及集群配置手册

本文将介绍如何搭建一个1-primary/1-secondary/1-arbiter的mongoDB集群

 1) 下载

至MongoDB官网下载：https://www.mongodb.org/downloads
选择当前稳定版本，选择合适的操作系统与优化版本。
以红帽6为例，选择RHEL 6 Linux 64-bit版本


 2) 安装

解压安装：

```
tar -xzf mongodb-linux-x86_64-rhel62-3.2.6.tgz
mv mongodb-linux-x86_64-rhel62-3.2.6 /home/ecwbs/mongodb
```

 3) 检查及修改ulimit配置
MongoDB对Linux的ulimit配置有要求，在运行MongoDB前，需先进行检查

查看当前ulimit配置：

```
ulimit -a
```

红色部分为MongoDB有要求的配置项，该项数值不应低于MongoDB的推荐配置

```
core file size (blocks, -c) 0
data seg size (kbytes, -d) unlimited
scheduling priority (-e) 0
file size (blocks, -f) unlimited
pending signals (-i) 7798
max locked memory (kbytes, -l) 64
max memory size (kbytes, -m) unlimited
open files (-n) 65535
pipe size (512 bytes, -p) 8
POSIX message queues (bytes, -q) 819200
real-time priority (-r) 0
stack size (kbytes, -s) 10240
cpu time (seconds, -t) unlimited
max user processes (-u) 7798
virtual memory (kbytes, -v) unlimited
file locks (-x) unlimited
```

MongoDB推荐配置：

```
* -f (file size): unlimited
* -t (cpu time): unlimited
* -v (virtual memory): unlimited
* -n (open files): 64000
* -m (memory size): unlimited
* -u (processes/threads): 64000
```
如果ulimit配置项当前值低于MongoDB推荐值，需要先进行修改

修改open files（需要root权限）

编辑/etc/security/limits.conf

在文件最后追加：

```
soft nofile 65535
hard nofile 65535
```

修改完成后，重新登录生效

修改其他ulimit参数

以修改max processes为例：

```
ulimit -u 65535
```

修改其他ulimit参数

以修改pending signals为例：

```
ulimit -q 80000
```

修改后仅在当前Shell中生效，所以请确保在当前Shell中启动MongoDB。如果退出重登录，需要重新进行设置。

修改其他系统参数配置

transparent_hugepage（需要root权限）

检查方法：

```
more /sys/kernel/mm/transparent_hugepage/enabled
more /sys/kernel/mm/transparent_hugepage/defrag
```

如果不为”never”，则需修改为never：

```
echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
echo "never" > /sys/kernel/mm/transparent_hugepage/defrag
```

修改后如果重新启动过主机，需要重新执行上述命令

zone_reclaim_mode（需要root权限）

检查方法：

```
more /proc/sys/vm/zone_reclaim_mode
```

如果不为0，则需修改为0：

```
echo 0 > /proc/sys/vm/zone_reclaim_mode
```
启动MongoDB实例

创建配置文件（文件名和路径任意）：

vi mongo.conf

```
 systemLog:
  destination: file
  ##日志存储位置
  path: /data/mongoDB/mongo.log
  logAppend: true
 storage:
  ##journal配置
  journal:
  enabled: true
  ##数据文件存储位置
  dbPath: /data/mongoDB/dbfile /
  ##是否一个库一个文件夹
  directoryPerDB: true
  ##数据引擎
  engine: wiredTiger
  ##WT引擎配置
  wiredTiger:
  engineConfig:
  ##缓存数据最大占用的内存
  cacheSizeGB: 20
  ##是否将索引也按数据库名单独存储
  directoryForIndexes: true
  ##索引配置
  indexConfig:
  prefixCompression: true
 ##是否作为Daemon进程运行
 processManagement:
  fork: true
 ##端口配置
 net:
  port: 27017
 ##集群配置
 replication:
  #集群名称
  replSetName: rs1
  #oplog大小
  oplogSizeMB: 250000
```

详细的配置说明，请参考https://docs.mongodb.org/manual/reference/

configuration-options/

创建日志与数据文件目录

```
mkdir /data
mkdir /data/mongoDB
mkdir /data/mongoDB/log
mkdir /data/mongoDB/dbfile
```

启动mongodb：

```
cd mongodb/bin
./mongod --config ./mongo.conf（此处应指定配置文件的路径）
```
红框部分代表启动成功

查看启动日志

至配置的systemLog.path路径下查看启动日志。

如果ulimit配置或系统参数配置有问题，在启动日志中会产生WARNING，如：

```
** WARNING: You are running this process as the root user, which is not recommended.
 ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
 ** We suggest setting it to 'never'
 ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
 ** We suggest setting it to 'never'
 ** WARNING: soft rlimits too low. rlimits set to 7798 processes, 65535 files. Number of processes should be at least 32767.5 : 0.5 times number of files.
```

按照前文中描述的方法一一解决即可

如果出现下面的告警信息

```
** WARNING: You are running on a NUMA machine.
 ** We suggest launching mongod like this to avoid performance problems:
 ** numactl –interleave=all mongod [other options]
```

则说明当前主机采用了NUMA架构，需要改以如下的命令启动：

```
numactl --interleave=all ./mongod --config ./mongo.conf
```

如果启动日志中没有任何WARNING和错误信息，则启动成功

启动其他两个mongoDB实例

在其他两台主机执行上述1-5步骤

配置MongoDB集群

登录至任意主机，执行mongodb/bin/mongo，进入mongo shell客户端

```
cd mongodb/bin
 ./mongo
```

在mongo shell中，定义集群配置对象：

```
config = {
  "_id" : "rs1",
  "members" : [
  {"_id":0,"host":"ip:port"},
  {"_id":1,"host":" ip:port "},
  {"_id":2,"host":" ip:port ", "arbiterOnly":true}
  ]
 }
```

其中，"_id" : "rs1"应与配置文件中配置的replicaction.replSetName一致，"members"中的"host"为三个节点的ip和端口号，第三个节点的"arbiterOnly":true代表其为仲裁节点。

初始化集群配置：

在mongo shell中执行：

```
rs.initiate(config)
```

如配置成功，shell会返回：

```
{ "ok" : 1 }
```

查看集群节点状态：

在mongo shell中执行：

```
rs.status()
```

Shell返回：

```
{
  "set" : "rs1",
  "date" : ISODate("2016-05-16T08:06:59.968Z"),
  "myState" : 1,
  "term" : NumberLong(4),
  "heartbeatIntervalMillis" : NumberLong(2000),
  "members" : [
  {
  "_id" : 0,
  "name" : "10.4.46.216:27017",
  "health" : 1,
  "state" : 1,
  "stateStr" : "PRIMARY",
  "uptime" : 3487,
  "optime" : {
  "ts" : Timestamp(1463383189, 1),
  "t" : NumberLong(4)
  },
  "optimeDate" : ISODate("2016-05-16T07:19:49Z"),
  "electionTime" : Timestamp(1463382817, 1),
  "electionDate" : ISODate("2016-05-16T07:13:37Z"),
  "configVersion" : 1,
  "self" : true
  },
  {
  "_id" : 1,
  "name" : "10.4.46.217:27017",
  "health" : 1,
  "state" : 2,
  "stateStr" : "SECONDARY",
  "uptime" : 3188,
  "optime" : {
  "ts" : Timestamp(1463383189, 1),
  "t" : NumberLong(4)
  },
  "optimeDate" : ISODate("2016-05-16T07:19:49Z"),
  "lastHeartbeat" : ISODate("2016-05-16T08:06:58Z"),
  "lastHeartbeatRecv" : ISODate("2016-05-16T08:06:58.380Z"),
  "pingMs" : NumberLong(0),
  "syncingTo" : "10.4.46.216:27017",
  "configVersion" : 1
  },
  {
  "_id" : 2,
  "name" : "10.4.46.215:27017",
  "health" : 1,
  "state" : 7,
  "stateStr" : "ARBITER",
  "uptime" : 3254,
  "lastHeartbeat" : ISODate("2016-05-16T08:06:58.236Z"),
  "lastHeartbeatRecv" : ISODate("2016-05-16T08:06:56.345Z"),
  "pingMs" : NumberLong(0),
  "configVersion" : 1
  }
  ],
  "ok" : 1
 }
```

关注stateStr字段，如集群配置正确，3个节点应有一个PRIMARY，一个SECONDARY，一个ARBITER。

如果stateStr字段显示为STARTUP或RECOVERING，说明集群状态还在同步中，稍等几秒后再次运行rs.status()，应该可以看到正确的结果。

配置集群安全认证
使用mongo shell连接到PRIMARY库

创建admin权限用户

```
use admin
 db.createUser({
  user:"username",
  pwd:"password",
  roles:[
  {role:"clusterAdmin", db:"admin"},
  {role:"dbAdminAnyDatabase", db:"admin"},
  {role:"readWriteAnyDatabase", db:"admin"},
  {role:"userAdminAnyDatabase", db:"admin"}
  ]
  })
```

上面的命令创建了一个具备集群管理者权限、所有数据库dba权限、所有数据库读写权限、所有数据库用户管理权限的超级用户

停止所有mongodb服务

```
ps –ef|grep mongod
kill -2 <pid>
```

注意：必须使用kill -2命令杀死mongod进程，否则会导致数据文件异常致使mongod无法再次正常启动

生成集群内部认证key

```
openssl rand -base64 755 > keyfile
chmod 400 keyfile
```

将keyfile拷贝至三台mongodb主机中（路径任意）

修改mongodb配置文件

在三台mongodb主机的mongodb配置文件（此例中mongo.conf），在文件最后追加：

```
security:
  keyFile: /home/ecwbs/mongodb/keyfile （此处配置key文件的绝对路径）
```

依次启动3个mongod实例

提示：启动前注意检查ulimit配置

使用mongo shell登入任意mongod实例
 身份认证：

```
use admin
db.auth(“username”,”password”)
```

认证通过后，再次执行：

```
rs.status()
```

应返回正确的集群状态


此后如果需要创建其他用户，先使用超级用户登录，再执行db.createUser命令即可

验证集群同步

验证主从节点数据自动同步：

由于从节点默认是不可读的，为验证数据同步，首先要将从节点设为可读：

使用mongo shell登录从节点，输入：

```
rs.slaveOk()
```

登录主节点，插入一条数据：

```
db.test1.insert({"test" : "ok"})
```

登录从节点，查找数据

```
db.test1.findOne()
```

Shell返回：

```
{ "_id" : ObjectId("56c5875a4c2c4c1d2acaccb7"), "test" : "ok" }
```

说明数据同步成功。

验证故障自动恢复

停止当前的主节点进程，然后连接从节点，执行命令

```
rs.status()
```

从输出中可以看到，原先的主节点已经不可用：




同时，先前的从节点已经升级为主节点：



说明故障自动恢复成功

此时在当前主节点上插入一条新数据：

```
db.test1.insert({"test2":"ok"})
```

将先前停止的节点启动，并用mongo shell连接

此时新启动的实例应为从节点，从其中查找数据：

```
db.test1.find()
```

Shell返回：

```
{ "_id" : ObjectId("56c5875a4c2c4c1d2acaccb7"), "test" : "ok" }
{ "_id" : ObjectId("56c58bcf18e5a473e209059f"), "test2" : "ok" }
```

验证故障节点恢复后，会自动从当前主节点同步增量数据

至此，mongodb高可用集群的配置已经完成

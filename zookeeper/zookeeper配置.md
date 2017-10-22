### 1.1 下载解压

解压到3个目录（模拟3台zk server）：

```
　　/home/hadoop/zookeeper-1

　　/home/hadoop/zookeeper-2

　　/home/hadoop/zookeeper-3
```

### 1.2 创建每个目录下conf/zoo.cfg配置文件 

/home/hadoop/zookeeper-1/conf/zoo.cfg 内容如下：

```
tickTime=2000

initLimit=10

syncLimit=5

dataDir=/home/hadoop/tmp/zk1/data

dataLogDir=/home/hadoop/tmp/zk1/log

clientPort=2181

server.1=localhost:2287:3387

server.2=localhost:2288:3388

server.3=localhost:2289:3389
```

/home/hadoop/zookeeper-2/conf/zoo.cfg 内容如下：

```
tickTime=2000

initLimit=10

syncLimit=5

dataDir=/home/hadoop/tmp/zk2/data

dataLogDir=/home/hadoop/tmp/zk2/log

clientPort=2182

server.1=localhost:2287:3387

server.2=localhost:2288:3388

server.3=localhost:2289:3389
```

/home/hadoop/zookeeper-3/conf/zoo.cfg 内容如下：

```
tickTime=2000

initLimit=10

syncLimit=5

dataDir=/home/hadoop/tmp/zk3/data

dataLogDir=/home/hadoop/tmp/zk3/log

clientPort=2183

server.1=localhost:2287:3387

server.2=localhost:2288:3388

server.3=localhost:2289:3389
```

注：因为是在一台机器上模拟集群，所以端口不能重复，这里用2181~2183，2287~2289，以及3387~3389相互错开。另外每个zk的instance，都需要设置独立的数据存储目录、日志存储目录，所以dataDir、dataLogDir这二个节点对应的目录，需要手动先创建好。


另外还有一个灰常关键的设置，在每个zk server配置文件的dataDir所对应的目录下，必须创建一个名为myid的文件，其中的内容必须与zoo.cfg中server.x 中的x相同，即：

```
/home/hadoop/tmp/zk1/data/myid 中的内容为1，对应server.1中的1

/home/hadoop/tmp/zk2/data/myid 中的内容为2，对应server.2中的2

/home/hadoop/tmp/zk3/data/myid 中的内容为3，对应server.3中的3
```

生产环境中，分布式集群部署的步骤与上面基本相同，只不过因为各zk server分布在不同的机器，上述配置文件中的localhost换成各服务器的真实Ip即可。分布在不同的机器后，不存在端口冲突问题，可以让每个服务器的zk均采用相同的端口，这样管理起来比较方便。


1.3 启动验证 

```
/home/hadoop/zookeeper-1/bin/zkServer.sh start


/home/hadoop/zookeeper-2/bin/zkServer.sh start


/home/hadoop/zookeeper-3/bin/zkServer.sh start
```

启用成功后，输入 jps 看下进程

```
20351 ZooKeeperMain

20791 QuorumPeerMain

20822 QuorumPeerMain

20865 QuorumPeerMain
```

应该至少能看到以上几个进程。


可以启动客户端测试下：

```
bin/zkCli.sh -server localhost:2181
```

(注：如果是远程连接，把localhost换成指定的IP即可)


成功后，应该会进到提示符下，类似下面这样:

```
[zk: localhost:2181(CONNECTED) 0]  
```

然后，就可以用一些基础命令，比如 ls ,create ,delete ,get 来测试了（关于这些命令，大家可以查看文档），特别提一个很有用的命令rmr 用来递归删除某个节点及其所有子节点

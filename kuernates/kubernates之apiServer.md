# Api Server功能

* 提供了集群管理的API接口
* 成为集群各个功能模块之间数据交互和通信的中心枢纽
* 拥有完备的集群安全机制


Apis Server除了kubectl命令来管理集群之外还可以通过REST接口来管理

```
/api/v1/proxy/nodes/{name}   {name}为节点的名称或者IP地址，由于该接口是REST接口
```

因此支持增删改查方法

列出节点内所有的pod信息

```
/api/v1/proxy/nodes/{name}/pods
```

列出节点内物理资源的统计信息

```
/api/v1/proxy/nodes/{name}/stats
```

列出节点概要信息

```
/api/v1/proxy/nodes/{name}/spec
```

在节点上运行某个容器，参考Docker的run命令

```
/api/v1/proxy/nodes/{name}/run
```

在节点上的某个容器运行某条命令，参考Docker的exec命令

```
/api/v1/proxy/nodes/{name}/exec
```

在节点上attach某个容器，参考Docker的attach命令

```
/api/v1/proxy/nodes/{name}/attach
```

实现节点上的Pod端口的转发

```
/api/v1/proxy/nodes/{name}/portForward
```

列出节点的各类日志信息，例如tallylog、lastlog、wtmp、ppp/、rhsm/、audit/、tuned/和anaconda/等

```
/api/v1/proxy/nodes/{name}/logs
```

列出和该节点相关的Metrics信息

```
/api/v1/proxy/nodes/{name}/metrics
```

列出节点内运行的pod信息

```
/api/v1/proxy/nodes/{name}/runningpods
```

列出节点内当前web服务器的状态，包括CPU占用情况和内存使用情况

```
/api/v1/proxy/nodes/{name}/debug/pprof
```

API Server不仅可以管理pod，而且可以通过API Server访问Pod提供的服务
访问Pod某个服务接口

```
/api/v1/namespaces/{namespace}/pods/{name}/proxy/{path:*}
```

访问pod的某个服务接口

```
/api/v1/proxy/namespaces/{namespace}/pods/{name}
```

例如

```
curl http://10.211.55.30:8080/api/v1/proxy/nodes/10.211.55.30/spec/
```
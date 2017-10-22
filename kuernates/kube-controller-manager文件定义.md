
## kube-controller-manager文件定义

```
--address=127.0.0.1
```

绑定主机IP地址，设置为0.0.0.0表示使用所有的网络地址

```
--port=10252
```

controller-manager监听主机端口号，默认为10252

```
--allocate-node-cidrs=false
```

设置为true表示使用云服务商为Pod分配的CIDRs，只在被托管的公有云中有效

```
--cloud-config=" "
```

云服务商的配置文件路径

```
--cloud-provider=" "
```

云服务商的名称

```
--cluster-cidr=<nil>
```

可用CIDR范围

```
--cluster-name="kubernetes"
```

Kubernetes集群名，也表现为实例名的前缀

```
--concurrent-endpoint-syncs=5
```

并发执行Endpoint同步操作的协程数，值越大标示同步操作越快，但将消耗更多的CPU和网络资源

```
--concurrent-rc-syncs=5
```

并发执行RC同步操作的协程数，值越大表示同步操作更快，但将消耗更多的CPU和网络资源

```
--deleting-pods-burst-10
```

如果一个Node节点失败，则会删除在上边运行的pod实例信息，此值定义了突发最大删除的Pod数量，与deleting-pods-qps一起作为调度中的限流因子

```
--deleting-pods-qps=0.1
```

当node失效时，每秒删除其上的多少pod实例。

```
--httptest.serve=
```

参见kube-apiserver中的参数说明

```
--kubeconfig=" "
```

Kubeconfig配置文件路径，在配置文件中包括Master地址信息及必要的认证信息

```
--master=" "
```

Kubenetes Master apiserver地址

```
--namespace-sync-period=5m0s
```

命名空间更新的同步时间间隔

```
--node-monitor-grace-period=40s
```

监控Node状态的时间间隔，超过该设置时间后，controller-manager会把Node标记为不可用状态。此值的设置有如下要求。

它应该被设置为Kubelet汇报Node状态时间间隔（参数--node-status-update-frequency=10s)的N倍，N标示kubelet状态汇报的重试次数。

``` 
 --node-monitor-period=5s
```
 
 通过NodeStatus的时间间隔

``` 
 --node-startup-grace-period=1m0s
```

 Node启动的最长允许时间，超过此时间无响应则会标记Node不可用状态（启动失败）

``` 
 --node-sync-period=10s
```

 Node信息发生变化是（例如新Node加入集群）controller-manager同步个Node信息的时间间隔

``` 
 --pod-eviction-timeout=5m0s
```

 在发现一个Node失效之后，延迟一段时间，在超过这个参数指定的时间后，删除此Node上的pod

``` 
 --profiling=true
```

打开性能分析，可以通过<host>:<port>/debug/pprof/地址查看栈、线程等系统运行信息

``` 
 --pvclaimbinder-sync-period=10s
```

 同步PV和PVC（容器声明的PV）的时间间隔

``` 
 --register-retry-count=10
```

 Node信息注册重试次数，默认为10次。重试的时间间隔为参数--node-sync-period的定义值。

```
 --resource-quota-sync-period=10s
```

 配额使用信息同步的时间间隔，默认为10秒

``` 
 --root-ca-file=" "
```

 跟CA证书文件路径，将被用于ServiceAccount的token secret中

``` 
 --service-account-private-key=file=" "
```

 用于给Service Account token签名的PEM-encoded RSA私钥文件路径

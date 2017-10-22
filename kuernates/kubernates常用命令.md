# kubernetes 常用命令

**获取组件状态**

```
# kubectl -s http://10.211.55.20:8080 get componentstatuses
结果
NAME STATUS MESSAGE ERROR
scheduler Healthy ok
controller-manager Healthy ok
etcd-0 Healthy {"health": "true"}
etcd-1 Healthy {"health": "true"}
```

**获取node的状态**

```
kubectl -s http://10.211.55.20:8080 get nodes
NAME STATUS AGE
10.211.55.26 Ready 13h
10.211.55.27 Ready 13h
```

**查看k8s版本**

```
kubectl -s 10.211.55.30:8080 version
Client Version: version.Info{Major:"1", Minor:"2", GitVersion:"v1.2.0", GitCommit:"ec7364b6e3b155e78086018aa644057edbe196e5", GitTreeState:"clean"}
Server Version: version.Info{Major:"1", Minor:"2", GitVersion:"v1.2.0", GitCommit:"ec7364b6e3b155e78086018aa644057edbe196e5", GitTreeState:"clean"}
```


**扩/缩容**

```
# kubectl scale rc redis-slave --replicas=3
```

**更新pod**

```
kubectl replace /path/ to/ my- pod. yaml
```

**查询pod  event**

```
kubectl get event
```

**查询容器日志**

```
$ kubectl create -f log- pod. yaml pod "log- pod" created
Pod 创建 成功 后， 会 重新 创建 异常 退出 的 容器 container2：

$ kubectl get pod log- pod NAME READY STATUS RESTARTS AGE log- pod 0/ 2 Error 1 19s
然后 分别 查询 Pod 中 两个 容器 的 日志：

$ kubectl logs log- pod container1
container1: 2015- 11- 21 14: 52: 55. 622701243+ 00: 00

$ kubectl logs log- pod container2
Pod "log- pod" in namespace "default": container "container2" is in waiting state.
```

Kubernetes 创建 Service 的 同时， 会 自动 创建 跟 Service 同名 的 Endpoints：

```
$ kubectl get endpoints my- nginx -o yaml
```


**根据yaml文件删除pod**

```
kubectl delete -f bjyd-dp-wls-dev.yaml
```

**根据yaml文件创建pod**

```
kubectl create -f bjyd-dp-wls-dev.yaml
```

**根据yaml文件扩容**

```
kubectl scale --replicas=3 -f bjyd-dp-wls-dev.yaml
```

**获得哦命名空间下所有pod，包含宿主机IP**

```
kubectl get pod --all-namespaces -o wide
```

**查询指定命名空间**

```
kubectl get pods --namespace=development  
```
**查看命名空间描述**

```
kubectl describe ns  XXX
```

**查看pod描述**

```
kubectl describe po  XXX
```

**查看service**

```
kubectl get service --all-namespaces
```




# Docker 常用命令

**拉取镜像**

```
docker pull busybox
```

**查看系统中存在的镜像**

-a 列出所有images(包含历史)

```
docker images
```

**删除一个或多个image**

```
docker rmi <image ID>**
```

**运行 busybox系统中的hello docker程序**

```
docker run busybox /bin/echo Hello Docker
```

**运行docker logs命令来查看job的当前状态：**

```
docker logs $sample_job**
```

**名为sample_job的容器，可以使用以下命令来停止：**

```
docker stop $sample_job
```

**重新启动该容器：**

```
docker restart $sample_job
```

**如果要完全移除容器，需要先将该容器停止，然后才能移除**
```
docker stop $sample_job
docker rm $sample_job
```

**将容器的状态保存为镜像**

```
docker commit $sample_job job1**
```

**获取服务器上镜像列表**

```
docker search tutorial
```

**获取指定名称镜像**

```
docker search 镜像名
```



**删除镜像/容器等**

```
$ rm -rf /var/lib/docker
```


**使用镜像创建容器 i：标记保证容器STDIN是开启的 -t:创建一个客户端**

```
docker run -i -t sauloal/ubuntu14.04

```

**创建一个容器，让其中运行 bash 应用，退出后容器关闭**

```
docker run -i -t sauloal/ubuntu14.04 /bin/bash
```

**创建一个名称centos_aways的容器，自动重启 # --restart参数：always始终重启；on-failure退出状态非0时重启；默认为，no不重启**

```
docker run -itd --name centos_aways --restart=always centos
```

**列出当前所有正在运行的container**

* -l 列出最近一次启动的container
* -a 列出所有container(包括历史，即运行过的container)
* -1 列出最近一次运行的container ID

```
docker ps
```

**操作容器**

* start 启动容器
* stop 停止容器
* restart 重启容器

```
docker start [container_id]
```

**进入正在运行的docker容器**

```
docker exec -it [container_id] /bin/bash
```

**映射 HOST 端口到容器，方便外部访问容器内服务，host_port 可以省略，省略表示把 container_port 映射到一个动态端口。**

```
docker run -i -t -p <host_port:contain_port>
```

**删除容器/删除所有的container**

```
docker rm <container...>
docker rm 'docker ps -a -q'
docker rm 'docker ps -a -q'
```

**将主机/data目录挂载到docker容器中/mnt下面**

```
docker run -v /data:/mnt -i -t 980e0e4c79ec /bin/bash
```

**根据57c312bbaad1创建镜像huangyong/javaweb:0.1**

```
docker commit 57c312bbaad1 huangyong/javaweb:0.1
```

**将docker容器中8080端口映射到主机上80端口**

```
docker run -i -t -p 90:8081 javaweb/centos:0.1
```


**进入已经启动的容器中**

```
docker attach 容器名
```

**访问docker客户端**

```
nc -U /var/run/docker.sock
```

**查看docker的运行状态**

```
status docker
```

**service命令管理docker进程**

* start 启动
* stop  停止
* status 状态
* restart 重启

```
service docker stop
```

**以守护模式运行docker程序**

```
docker -d
```

**查看docker信息**

```
docker info
```


**使用h配置启动选项**

```
docker -H
```

**卸载docker**

* 查找安装包

```
$ yum list installed | grep docker
yum list installed | grep docker
docker-engine.x86_64   1.7.1-1.el7 @/docker-engine-1.7.1-1.el7.x86_64.rpm
```

* 删除安装包

```
$ sudo yum -y remove docker-engine.x86_64
```

**导出docker 镜像**

```
docker save -o busybox > busybox.tar
```

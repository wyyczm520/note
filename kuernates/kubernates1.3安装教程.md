**版本搭配**

```
docker_1.10.3 + etcd_2.3.7 + kubernetes-master _1.3.0
```

**系统：**

```
centos7-everything  
```

**三台虚拟机：**

```
 10.211.55.30 master  node  此机器既是master又是 node
 10.211.55.31 node
 10.211.55.32 node
```

---

### 背景知识 

**1.docker常用命令**

查看当前系统镜像

```
docker images 
```

导入镜像

```
docker load -i 
```

导出镜像

```
docker save -o xxx.tar  xxx路径   
```

删除镜像

```
docker rmi 
```

查看当前运行的镜像

```
docker ps 
```

**2.kubernates常用操作**

查看所有的pod
kubectl get pods --all-namesapces  
查看默认命名空间下的pod
kubectl get pods  
查看pod状态
kubectl describe pod 容器名称     
创建pod、创建serivice 、创建namespace都是用的此命令
kubectl create -f xxx.yaml 
删除pod 、删除service、删除namespace都是用的此命令
kubectl delete -f xxx.yaml 
查看所有命名空间下的service
kubectl get services --all-namespaces  
查看默认命名空间下的services
kubectl get services 
 查看service创建的描述
kubectl describe get svc 名称 
查看命名空间

kubectl get namespaces 
动态扩容
kubectl scale --replicas=1 -f xxxx.yaml  
使用命令创建service

kubectl expose deployment my-nginx --target-port=80 --port=8083 --protocol=TCP --type=NodePort  
kubectl get node  查看node状态

------------------环境设置----------

1】关闭防火墙
systemctl stop firewalld.service
systemctl disable firewalld.service

setenforce 0
查看状态getenforce 
结果为 Permissive为正常

2】修改hosts
执行命令vi /etc/hosts
10.211.55.30 10-211-55-30.master
127.0.0.1 10-211-55-30.master

3】修改主机名
执行命令 vi /etc/hostname
10-211-55-30.master

4】修改内核的hostname
sysctl kernel.hostname=10-211-55-30.master 

------------配置master 10.211.55.30 主节点-------------------
1】 更新依赖
	yum update  

2】安装etcd kubernetes-master和docker
	yum install etcd kubernetes-master docker -y
	安装日志参见【日志1】
	
3】设置 etcd参数 ，10.211.55.30这个IP需要根据自己的实际情况进行修改
vi /etc/etcd/etcd.conf
ETCD_NAME=default
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"
ETCD_ADVERTISE_CLIENT_URLS="http://0.0.0.0:2379"

4】注册etcd服务、并且启动	
systemctl enable etcd
systemctl start etcd

5】向etcd中写入信息
etcdctl set coreos.com/network/config '{"Network":"172.17.0.0/16"}'
	
6】设置 kubernetes server的参数/etc/kubernetes/apiserver  ,192.168.0.0/16 可以根据自己情况设置，也可以默认

KUBE_API_ADDRESS="--insecure-bind-address=0.0.0.0"
KUBE_API_PORT="--port=8080"
KUBELET_PORT="--kubelet-port=10250"
KUBE_ETCD_SERVERS="--etcd-servers=http://10.211.55.30:2379"
KUBE_SERVICE_ADDRESSES="--service-cluster-ip-range=10.254.0.0/16"
KUBE_ADMISSION_CONTROL="--admission-control=NamespaceLifecycle,NamespaceExists,LimitRanger,SecurityContextDeny,ResourceQuota"
KUBE_API_ARGS="--secure-port=0"


7】设置/etc/kubernetes/controller-manager文件里的参数
KUBE_CONTROLLER_MANAGER_ARGS="--node-monitor-grace-period=10s --pod-eviction-timeout=10s"
KUBE_MASTER="http://10.211.55.30:8080"

8】设置/etc/kubernetes/config 文件里的参数
KUBE_LOGTOSTDERR="--logtostderr=true"
KUBE_ETCD_SERVERS="http://10.211.55.30:2379"
KUBE_LOG_LEVEL="--v=0"
KUBE_ALLOW_PRIV="--allow-privileged=false"
KUBE_MASTER="--master=http://10.211.55.30:8080"

8】启动k8s服务
设置系统服务 systemctl enable kube-apiserver kube-scheduler kube-controller-manager
启动系统服务 systemctl start  kube-apiserver kube-scheduler kube-controller-manager

使用 journalctl -f  查看systemctl日志信息

------------配置node 10.211.55.30 10.211.55.31  10.211.55.32节点-------------------
1】安装kubernetes-node节点和 flannel docker组件
	yum install kubernetes-node flannel docker -y
	日志信息参见【日志2】

2】设置docker 为系统服务、并且启动
systemctl enable docker
systemctl start docker

3】设置 /etc/sysconfig/flanneld文件

FLANNEL_ETCD="http://10.211.55.30:2379"
FLANNEL_ETCD_KEY="/coreos.com/network"
FLANNEL_OPTIONS="--iface=eth0"

4】注册、启动flanneld服务，重启docker 
systemctl enable flanneld   
systemctl restart flanneld  docker  

5】设置 /etc/kubernetes/config
KUBE_LOGTOSTDERR="--logtostderr=true"
KUBE_LOG_LEVEL="--v=0"
KUBE_ETCD_SERVERS="http://10.211.55.30:2379"
KUBE_ALLOW_PRIV="--allow-privileged=false"
KUBE_MASTER="--master=http://10.211.55.30:8080"

6】设置 /etc/kubernetes/kubelet文件
KUBELET_ADDRESS="--address=0.0.0.0"
KUBELET_PORT="--port=10250"
KUBELET_HOSTNAME="--hostname-override=10.211.55.31"
KUBELET_API_SERVER="--api-servers=http://10.211.55.30:8080"
KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=registry.access.redhat.com/rhel7/pod-infrastructure:latest"
KUBELET_ARGS=""

7】设置、启动 kubelet kube-proxy系统服务
systemctl enable kubelet kube-proxy
systemctl start kubelet kube-proxy

8】安装完成后可以在主节点上执行命令kubectl get nodes查看节点状态

启动节点都按照这样的设置就可以完成
完成好上述设置后我们在kubernetes server节点也就是我们实验中的host 192.168.163.148中执行kubectl get nodes可以看到如下节点：



-----------------安装pod------------------

1.需要导入三个镜像 docker load -i XXX.tar  ，参见【images】文件夹下文件
pod-infrastructure.tar
qinyujia-test-nginx.tar
kubernetes-dashboard-amd64_v1.4.0.tar

导入完成之后使用 docker images命令查看是否有此镜像

2.创建pod  执行命令kubectl create -f xxx.yaml  ，文件夹【yaml文件】下有

执行完成之后使用 kubectl get pods --all-namespace查看是否 RUNNING

3. 创建service 执行命令kubectl create -f xxx.yaml  ，文件夹【yaml文件】下有

执行完成之后使用 kubectl get svc --all-namespace查看是否 RUNNING

4.最后可以使用curl  访问pod中的地址

安装kube-ui也按照上边的步骤

[TOC]
### 背景知识

**1.kubernets和 docker的版本搭配是**

```
kubernates1.5.1 + docker 1.12.1
```

**2.tmux命令使用**

查看tmux列表  

```
tmux ls
```

进入tmux窗口  ，前一步查看出来的id

```
tmux attach -t 0
```

**3.docker常用命令**

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
docker save -o xxx.tar  
```

删除镜像

```
docker rmi
```

查看当前运行的镜像

```
docker ps
```

**4.kubernates常用操作**

查看所有的pod

```
kubectl get pods --all-namesapces  
```

查看默认命名空间下的pod

```
kubectl get pods  
```

容器名称     

```
kubectl describe pod
```

创建pod、创建serivice 、创建namespace都是用的此命令

```
kubectl create -f xxx.yaml
```

删除pod 、删除service、删除namespace都是用的此命令

```
kubectl delete -f xxx.yaml
```

查看所有命名空间下的service

```
kubectl get services --all-namespaces  
```

查看默认命名空间下的services

```
kubectl get services
```

查看service创建的描述

```
kubectl describe get svc 名称
```

查看命名空间

```
kubectl get namespaces
```

动态扩容

```
kubectl scale --replicas=1 -f xxxx.yaml  
```

使用命令创建service

```
kubectl expose deployment my-nginx --target-port=80 --port=8083 --protocol=TCP --type=NodePort
```

查看node状态

```
kubectl get node  

```

删除node

```
kubectl delete node  

```

查看日志

```
journalctl -f

```

----
### 环境设置

1.关闭防火墙

```
systemctl stop firewalld.service
setenforce 0
```

查看状态

```
getenforce
```

结果为

```
Permissive为正常
```

2.修改hosts


执行命令

```
vi /etc/hosts
10.211.55.30 10-211-55-30.master
127.0.0.1 10-211-55-30.master
```

3.修改主机名

执行命令

```
vi /etc/hostname
10-211-55-30.master
```

4.修改内核的hostname

```
sysctl kernel.hostname=10-211-55-30.master
```

5.查询网关，网关的IP下边的步骤需要用到

```
netstat -rn
显示结果
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         10.211.55.1     0.0.0.0         UG        0 0          0 eth0
10.211.55.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 docker0
192.168.122.0   0.0.0.0         255.255.255.0   U         0 0          0 virbr0
```

6.设置静态IP

路径 ```/etc/sysconfig/network-scripts/ifcfg-eth0```

添加内容

```
IPADDR=10.211.55.30
NETMASK=255.255.255.0
GATEWAY=10.211.55.255
BOOTPROTO=static 这个不是新加，是修改
```

---

### 在线安装

**在线更新yum源**

```
地址：http://mirrors.aliyun.com/repo/
```

**设置新的yum源**

```
mv Centos-7.repo  /etc/yum.repos.d/
```

**指定docker 镜像源**

```
curl  -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -
```

---

### 离线安装

#### 一：安装master节点

**1.安装docker，需要联网**

```
yum install  docker-engine-selinux-1.12.1-1.el7.centos.noarch.rpm
yum install docker-engine-1.12.1-1.el7.centos.x86_64.rpm
```

坑：如果找不到镜像地址的话，需要添加一个DNS

```
vi /etc/resolv.conf
```

内容```nameserver 8.8.8.8``` 或者```114.114.114.114```

**2.启动docker**

```
service docker start
```

查看启动状态，如果有客户端和服务端证明成功

```
docker version
```

**3.向docker中导入1.5版本镜像28个，执行下边命令，一个一个执行**

```
docker load -i busyboxgoogle.tar
docker load -i busybox.tar
docker load -i calicocni.tar
docker load -i calicoctl.tar
docker load -i calico-kube-policy-controller.tar
docker load -i caliconode.tar
docker load -i dnsmasq-metrics.tar
docker load -i dnssidecar.tar
docker load -i etcd-amd64.tar
docker load -i exechealthz-amd64111.tar
docker load -i exechealthz-amd64.tar
docker load -i kube-apiserver-amd64.tar
docker load -i kube-apiserver.tar
docker load -i kube-controller-manager-amd64.tar
docker load -i kube-controller-manager.tar
docker load -i kube-discovery.tar
docker load -i kubedns-amd64.tar
docker load -i kube-dnsmasq-amd6414.tar
docker load -i kube-dnsmasq-amdd.tar
docker load -i kubedns.tar
docker load -i kube-proxy140.tar
docker load -i kube-proxy-amd64.tar
docker load -i kube-proxy.tar
docker load -i kubernetes-dashboard-amd64.tar
docker load -i kube-scheduler.tar
docker load -i nginx111.tar
docker load -i nginx.tar
docker load -i pause-amd645.tar
```

**4.安装k8s**

执行命令```yum install *.rpm -y``` ，安装以下4个包

```
kubeadm-1.6.0-0.alpha.0.2074.a092d8e0f95f52.x86_64.rpm
kubectl-1.5.1-0.x86_64.rpm
kubelet-1.5.1-0.x86_64.rpm
kubernetes-cni-0.3.0.1-0.07a8a2.x86_64.rpm
```

**5.安装etcd**

移除系统默认的k8s配置文件

```
rm -rf /etc/kubernetes/
rm -rf /var/lib/kubelet/
```

解压etcd目录

```
tar -zxvf etcd****
```

安装tmux，执行命令

```
yum install tmux
```

执行tmux进入后台场景  ```control+b``` 然后按d退出tmux模式

查看etcd.txt中的执行脚本，修改配置参数IP、infra0等,

修改一下脚本

```
./etcd --name infra0 --initial-advertise-peer-urls http://10.211.55.30:2380 \
      --listen-peer-urls http://10.211.55.30:2380 \
      --listen-client-urls http://10.211.55.30:2379,http://127.0.0.1:2379 \
      --advertise-client-urls http://10.211.55.30:2379 \
      --initial-cluster-token etcd-cluster-1 \
      --initial-cluster infra0=http://10.211.55.30:2380,infra1=http://10.211.55.31:2380,infra2=http://10.211.55.32:2380 \
      --initial-cluster-state new

./etcd --name infra1 --initial-advertise-peer-urls http://10.211.55.31:2380 \
      --listen-peer-urls http://10.211.55.31:2380 \
      --listen-client-urls http://10.211.55.31:2379,http://127.0.0.1:2379 \
      --advertise-client-urls http://10.211.55.31:2379 \
      --initial-cluster-token etcd-cluster-1 \
      --initial-cluster infra0=http://10.211.55.30:2380,infra1=http://10.211.55.31:2380,infra2=http://10.211.55.32:2380 \
      --initial-cluster-state new

./etcd --name infra2 --initial-advertise-peer-urls http://10.211.55.32:2380 \
      --listen-peer-urls http://10.211.55.32:2380 \
      --listen-client-urls http://10.211.55.32:2379,http://127.0.0.1:2379 \
      --advertise-client-urls http://10.211.55.32:2379 \
      --initial-cluster-token etcd-cluster-1 \
      --initial-cluster infra0=http://10.211.55.30:2380,infra1=http://10.211.55.31:2380,infra2=http://10.211.55.32:2380 \
      --initial-cluster-state new
```

**6.初始化k8s**

```
kubeadm init --api-advertise-addresses=10.211.55.30 --external-etcd-endpoints=http://10.211.55.30:2379,http://10.211.55.31:2379,http://10.211.55.32:2379 --use-kubernetes-version v1.5.1
展示信息如下
Flag --external-etcd-endpoints has been deprecated, this flag will be removed when componentconfig exists
[kubeadm] WARNING: kubeadm is in alpha, please do not use it for production clusters.
[preflight] Running pre-flight checks
[preflight] WARNING: kubelet service is not enabled, please run 'systemctl enable kubelet.service'
[preflight] WARNING: docker service is not enabled, please run 'systemctl enable docker.service'
[preflight] Starting the kubelet service
[init] Using Kubernetes version: v1.5.1
[tokens] Generated token: "c69530.57326a39f661d33c"
[certificates] Generated Certificate Authority key and certificate.
[certificates] Generated API Server key and certificate
[certificates] Generated Service Account signing keys
[certificates] Created keys and certificates in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/admin.conf"
[apiclient] Created API client, waiting for the control plane to become ready
[apiclient] All control plane components are healthy after 19.897967 seconds
[apiclient] Waiting for at least one node to register and become ready
[apiclient] First node is ready after 3.009735 seconds
[apiclient] Creating a test deployment
[apiclient] Test deployment succeeded
[token-discovery] Created the kube-discovery deployment, waiting for it to become ready
[token-discovery] kube-discovery is ready after 3.010512 seconds
[addons] Created essential addon: kube-proxy
[addons] Created essential addon: kube-dns

Your Kubernetes master has initialized successfully!

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
    http://kubernetes.io/docs/admin/addons/

You can now join any number of machines by running the following on each node:

kubeadm join --token=c69530.57326a39f661d33c 10.211.55.30  ##############此处判定为启动成功
```

**7.组网**

当前的安装方案使用的是calico的组网方案，到此时dns的pod服务应该是启动失败的，此时需要运行calico.yaml的文件,切记切记：要改里边的IP  这里使用的是这个文件beijing-calico.yaml


#### 二：安装node节点

需要执行环境变量

**1.安装docker，需要联网**

```
yum install  docker-engine-selinux-1.12.1-1.el7.centos.noarch.rpm
yum install docker-engine-1.12.1-1.el7.centos.x86_64.rpm
```

坑：如果找不到镜像地址的话，需要添加一个DNS

```
vi /etc/resolv.conf
```

内容```nameserver 8.8.8.8``` 或者```114.114.114.114```

**2.启动docker**

```
service docker start
```

查看启动状态，如果有客户端和服务端证明成功

```
docker version
```

**3.向docker中导入1.5版本镜像28个，执行下边命令，一个一个执行**

```
docker load -i busyboxgoogle.tar
docker load -i busybox.tar
docker load -i calicocni.tar
docker load -i calicoctl.tar
docker load -i calico-kube-policy-controller.tar
docker load -i caliconode.tar
docker load -i dnsmasq-metrics.tar
docker load -i dnssidecar.tar
docker load -i etcd-amd64.tar
docker load -i exechealthz-amd64111.tar
docker load -i exechealthz-amd64.tar
docker load -i kube-apiserver-amd64.tar
docker load -i kube-apiserver.tar
docker load -i kube-controller-manager-amd64.tar
docker load -i kube-controller-manager.tar
docker load -i kube-discovery.tar
docker load -i kubedns-amd64.tar
docker load -i kube-dnsmasq-amd6414.tar
docker load -i kube-dnsmasq-amdd.tar
docker load -i kubedns.tar
docker load -i kube-proxy140.tar
docker load -i kube-proxy-amd64.tar
docker load -i kube-proxy.tar
docker load -i kubernetes-dashboard-amd64.tar
docker load -i kube-scheduler.tar
docker load -i nginx111.tar
docker load -i nginx.tar
docker load -i pause-amd645.tar
```

**4.安装k8s**

执行命令yum install *.rpm -y ，安装以下4个包

```
kubeadm-1.6.0-0.alpha.0.2074.a092d8e0f95f52.x86_64.rpm
kubectl-1.5.1-0.x86_64.rpm
kubelet-1.5.1-0.x86_64.rpm
kubernetes-cni-0.3.0.1-0.07a8a2.x86_64.rpm
```

**5.安装完成后执行master节点安装成功的语句进行，添加节点，进行组网**

```

kubeadm join --token=c69530.57326a39f661d33c 10.211.55.30  此处判定为启动成功
```

---


### 备注

本地机器启动后的进程

**1.【主节点启动后的程序】**

```
[root@10-211-55-30 k8s]# ps -ef | grep ku
postfix  10997  2287  0 22:27 ?        00:00:00 pickup -l -t unix -u
root     12178 12160  4 23:45 ?        00:00:02 kube-controller-manager --address=127.0.0.1 --leader-elect --master=127.0.0.1:8080 --cluster-name=kubernetes --root-ca-file=/etc/kubernetes/pki/ca.pem --service-account-private-key-file=/etc/kubernetes/pki/apiserver-key.pem --cluster-signing-cert-file=/etc/kubernetes/pki/ca.pem --cluster-signing-key-file=/etc/kubernetes/pki/ca-key.pem --insecure-experimental-approve-all-kubelet-csrs-for-group=system:kubelet-bootstrap
root     12235 12224 11 23:45 ?        00:00:06 kube-apiserver --insecure-bind-address=127.0.0.1 --admission-control=NamespaceLifecycle,LimitRanger,ServiceAccount,PersistentVolumeLabel,DefaultStorageClass,ResourceQuota --service-cluster-ip-range=10.96.0.0/12 --service-account-key-file=/etc/kubernetes/pki/apiserver-key.pem --client-ca-file=/etc/kubernetes/pki/ca.pem --tls-cert-file=/etc/kubernetes/pki/apiserver.pem --tls-private-key-file=/etc/kubernetes/pki/apiserver-key.pem --token-auth-file=/etc/kubernetes/pki/tokens.csv --secure-port=6443 --allow-privileged --advertise-address=10.211.55.30 --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --anonymous-auth=false --etcd-servers=http://10.211.55.30:2379,http://10.211.55.31:2379,http://10.211.55.32:2379
root     12319 12304  1 23:45 ?        00:00:00 /kube-dns --domain=cluster.local --dns-port=10053 --config-map=kube-dns --v=2
root     12643 12624  3 23:45 ?        00:00:01 kube-proxy --kubeconfig=/run/kubeconfig
root     13086 13068  0 23:45 ?        00:00:00 /exechealthz --cmd=nslookup kubernetes.default.svc.cluster.local 127.0.0.1 >/dev/null --url=/healthz-dnsmasq --cmd=nslookup kubernetes.default.svc.cluster.local 127.0.0.1:10053 >/dev/null --url=/healthz-kubedns --port=8080 --quiet
root     13324 13301  1 23:45 ?        00:00:00 kube-scheduler --address=127.0.0.1 --leader-elect --master=127.0.0.1:8080
root     13406 13390  0 23:45 ?        00:00:00 /usr/local/bin/kube-discovery
root     14608 22940  0 23:46 pts/2    00:00:00 grep --color=auto ku
root     18527     1  7 21:01 ?        00:12:05 /usr/bin/kubelet --kubeconfig=/etc/kubernetes/kubelet.conf --require-kubeconfig=true --pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true --network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin --cluster-dns=10.96.0.10 --cluster-domain=cluster.local
root     18375 17705  4 21:00 pts/1    00:08:02 ./etcd --name infra0 --initial-advertise-peer-urls http://10.211.55.30:2380 --listen-peer-urls http://10.211.55.30:2380 --listen-client-urls http://10.211.55.30:2379,http://127.0.0.1:2379 --advertise-client-urls http://10.211.55.30:2379 --initial-cluster-token etcd-cluster-1 --initial-cluster infra0=http://10.211.55.30:2380,infra1=http://10.211.55.31:2380,infra2=http://10.211.55.32:2380 --initial-cluster-state new
root     18811 22940  0 23:50 pts/2    00:00:00 grep --color=auto etcd
```

**2.【node节点启动后显示的进程】**

```
[root@10-211-55-31 etcd-v2.2.1-linux-amd64]# ps -ef | grep ku
root     27279     1  4 22:39 ?        00:03:15 /usr/bin/kubelet --kubeconfig=/etc/kubernetes/kubelet.conf --require-kubeconfig=true --pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true --network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin --cluster-dns=10.96.0.10 --cluster-domain=cluster.local
root     27564 27547  2 22:39 ?        00:01:55 kube-proxy --kubeconfig=/run/kubeconfig
postfix  30933  2406  0 23:48 ?        00:00:00 pickup -l -t unix -u
root     31249 13704  0 23:48 pts/0    00:00:00 grep --color=auto ku
root     18375 17705  4 21:00 pts/1    00:08:02 ./etcd --name infra0 --initial-advertise-peer-urls http://10.211.55.30:2380 --listen-peer-urls http://10.211.55.30:2380 --listen-client-urls http://10.211.55.30:2379,http://127.0.0.1:2379 --advertise-client-urls http://10.211.55.30:2379 --initial-cluster-token etcd-cluster-1 --initial-cluster infra0=http://10.211.55.30:2380,infra1=http://10.211.55.31:2380,infra2=http://10.211.55.32:2380 --initial-cluster-state new
root     18811 22940  0 23:50 pts/2    00:00:00 grep --color=auto etcd
```

**3.如果中间有问题，有可能是系统防火墙没关，需要清除下目录**

```
rm -rf /var/lib/kubelet/
rm -rf /etc/kubernetes/
```

**4.删除docker映射磁盘失败**

```
[root@10-4-46-212 lib]# cat /proc/mounts | grep docker
/dev/mapper/vg00-lv_root /var/lib/docker/devicemapper ext4 rw,relatime,data=ordered 0 0
[root@10-4-46-212 lib]# umount /var/lib/docker/devicemapper
```
# Flannel介绍

Flannel是 CoreOS 团队针对 Kubernetes 设计的一个覆盖网络（Overlay Network）工具，其目的在于帮助每一个使用 Kuberentes 的 CoreOS 主机拥有一个完整的子网。这次的分享内容将从Flannel的介绍、工作原理及安装和配置三方面来介绍这个工具的使用方法。

## 第一部分：Flannel介绍
Flannel是CoreOS团队针对Kubernetes设计的一个网络规划服务，简单来说，它的功能是让集群中的不同节点主机创建的Docker容器都具有全集群唯一的虚拟IP地址。

在Kubernetes的网络模型中，假设了每个物理节点应该具备一段“属于同一个内网IP段内”的“专用的子网IP”。例如：

节点A：10.0.1.0/24

节点B：10.0.2.0/24

节点C：10.0.3.0/24

但在默认的Docker配置中，每个节点上的Docker服务会分别负责所在节点容器的IP分配。这样导致的一个问题是，不同节点上容器可能获得相同的内外IP地址。并使这些容器之间能够之间通过IP地址相互找到，也就是相互ping通。

Flannel的设计目的就是为集群中的所有节点重新规划IP地址的使用规则，从而使得不同节点上的容器能够获得“同属一个内网”且”不重复的”IP地址，并让属于不同节点上的容器能够直接通过内网IP通信。

# 第二部分：Flannel的工作原理
Flannel实质上是一种“覆盖网络(overlay network)”，也就是将TCP数据包装在另一种网络包里面进行路由转发和通信，目前已经支持UDP、VxLAN、AWS VPC和GCE路由等数据转发方式。

默认的节点间数据通信方式是UDP转发，在Flannel的GitHub页面有如下的一张原理图：

<img src="./img/2.1.png" />

这张图的信息量很全，下面简单的解读一下。

数据从源容器中发出后，经由所在主机的docker0虚拟网卡转发到flannel0虚拟网卡，这是个P2P的虚拟网卡，flanneld服务监听在网卡的另外一端。

Flannel通过Etcd服务维护了一张节点间的路由表，在稍后的配置部分我们会介绍其中的内容。

源主机的flanneld服务将原本的数据内容UDP封装后根据自己的路由表投递给目的节点的flanneld服务，数据到达以后被解包，然后直接进入目的节点的flannel0虚拟网卡，然后被转发到目的主机的docker0虚拟网卡，最后就像本机容器通信一下的有docker0路由到达目标容器。

这样整个数据包的传递就完成了，这里需要解释三个问题。

第一个问题，UDP封装是怎么一回事？

我们来看下面这个图，这是在其中一个通信节点上抓取到的ping命令通信数据包。可以看到在UDP的数据内容部分其实是另一个ICMP（也就是ping命令）的数据包。

<img src="./img/2.2.png" />

原始数据是在起始节点的Flannel服务上进行UDP封装的，投递到目的节点后就被另一端的Flannel服务还原成了原始的数据包，两边的Docker服务都感觉不到这个过程的存在。

第二个问题，为什么每个节点上的Docker会使用不同的IP地址段？

这个事情看起来很诡异，但真相十分简单。其实只是单纯的因为Flannel通过Etcd分配了每个节点可用的IP地址段后，偷偷的修改了Docker的启动参数，见下图。

<img src="./img/2.3.png" />

这个是在运行了Flannel服务的节点上查看到的Docker服务进程运行参数。

注意其中的“--bip=172.17.18.1/24”这个参数，它限制了所在节点容器获得的IP范围。

这个IP范围是由Flannel自动分配的，由Flannel通过保存在Etcd服务中的记录确保它们不会重复。

第三个问题，为什么在发送节点上的数据会从docker0路由到flannel0虚拟网卡，在目的节点会从flannel0路由到docker0虚拟网卡？

我们来看一眼安装了Flannel的节点上的路由表。下面是数据发送节点的路由表：

<img src="./img/2.4.png" />

这个是数据接收节点的路由表：

<img src="./img/2.5.png" />

例如现在有一个数据包要从IP为172.17.18.2的容器发到IP为172.17.46.2的容器。根据数据发送节点的路由表，它只与172.17.0.0/16匹配这条记录匹配，因此数据从docker0出来以后就被投递到了flannel0。同理在目标节点，由于投递的地址是一个容器，因此目的地址一定会落在docker0对于的172.17.46.0/24这个记录上，自然的被投递到了docker0网卡。

## 第三部分：Flannel的安装和配置

Flannel是Golang编写的程序，因此的安装十分简单。

从https://github.com/coreos/flannel/releases和https://github.com/coreos/etcd/releases分别下载Flannel和Etcd的最新版本二进制包。

解压后将Flannel的二进制文件“flanneld”和脚本文件“mk-docker-opts.sh”、以及Etcd的二进制文件“etcd”和“etcdctl”放到系统的PATH目录下面安装就算完成了。

配置部分要复杂一些。

首先启动Etcd，参考https://github.com/coreos/etcd ... overy。

访问这个地址：https://discovery.etcd.io/new?size=3 获得一个“Discovery地址”

在每个节点上运行以下启动命令：

```
etcd -initial-advertise-peer-urls http://<当前节点IP>:2380 -listen-peer-urls http://<当前节点IP>:2380 -listen-client-urls http://<当前节点IP>:2379,http://<当前节点IP>:2379 -advertise-client-urls http://<当前节点IP>:2379 -discovery <刚刚获得的Discovery地址> &
```

启动完Etcd以后，就可以配置Flannel了。

Flannel的配置信息全部在Etcd里面记录，往Etcd里面写入下面这个最简单的配置，只指定Flannel能用来分配给每个Docker节点的拟IP地址段：
etcdctl set /coreos.com/network/config '{ "Network": "172.17.0.0/16" }'

然后在每个节点分别启动Flannel：

```
flanneld &
```

最后需要给Docker动一点手脚，修改它的启动参数和docker0地址。

在每个节点上执行：

```
sudo mk-docker-opts.sh -i
source /run/flannel/subnet.env
sudo rm /var/run/docker.pid
sudo ifconfig docker0 ${FLANNEL_SUBNET}
```

重启动一次Docker，这样配置就完成了。

现在在两个节点分别启动一个Docker容器，它们之间已经通过IP地址直接相互ping通了。

到此，整个Flannel集群也就正常运行了。

最后，前面反复提到过Flannel有一个保存在Etcd的路由表，可以在Etcd数据中找到这些路由记录，如下图。

<img src="./img/2.6.png" />

## Q&A

**问：数据从源容器中发出后，经由所在主机的docker0虚拟网卡转发到flannel0虚拟网卡，这种P2P实际生产中是否存在丢包，或者此机制有高可用保障么？**

答：只是本机的P2P网卡，没有经过外部网络，应该还比较稳定。但我这里没有具体数据。

**问：UDP数据封装，转发的形式也是UDP么？我们一般知道UDP发送数据是无状态的，可靠么？**

答：转发的是UDP，高并发数据流时候也许会有问题，我这里同样没有数据。

**问：实际上，kubernates是淡化了容器ip，外围用户只需关注所调用的服务，并不关心具体的ip，这里fannel将IP分开且唯一，这样做有什么好处？有实际应用的业务场景么？**

答： IP唯一是Kubernetes能够组网的条件之一，不把网络拉通后面的事情都不好整。

**问：Flannel通过Etcd分配了每个节点可用的IP地址段后，偷偷的修改了Docker的启动参数：那么如果增加节点，或删除节点，这些地址段（ETCD上）会动态变化么？如果不是动态变化，会造成IP地址的浪费么？**

答会造成一些浪费，一般使用10.x.x.x的IP段。

**问：sudo mk-docker-opts.sh -i 这个命令具体干什么了？非coreos上使用flannel有什么不同？**

答：生成了一个Docker启动的环境变量文件，里面给Docker增加了启动参数。
没有什么不同，只是CoreOS集成了Flannel，在CoreOS上面启动Flannel只是一行命令：systemctl start flanneld。

**问：容器IP都是固定的吗？外网与物理主机能ping通，也能ping通所有Docker集群的容器IP？**

答：不是固定的，IP分配还是Docker在做，Flannel只是分配了子网。

**问：Flannel的能否实现VPN？你们有没有研究过？**

答： 应该不能，它要求这些容器本来就在一个内网里面。

**问：Flannl是谁开发的？全是对k8s的二次开发吗？**

答： CoreOS公司，不是k8s的二次开发，独立的开源项目，给k8s提供基础网络环境。

**问：Flannel支持非封包的纯转发吗？这样性能就不会有损失了？**

答：非封装怎样路由呢？发出来的TCP包本身并没有在网络间路由的信息，别忘了，两个Flannel不是直连的，隔着普通的局域网络。

**问： Flanel现在到哪个版本了，后续版本有什么侧重点？性能优化，还是功能扩展？**
答：还没到1.0，在GitHub上面有他们的发展计划，性能是很大的一部分。

**问： 就是在CoreOS中，客户还需要安装Flannel吗？**

答：不需要，在启动的Cloudinit配置里面给Etcd写入Flannel配置，然后加上flanneld.service command: start 就可以了，启动完直接可用，文档连接我不找了，有这段配置，现成的。

**问： 可不可以直接用命令指定每个主机的ip范围，然后做gre隧道实现节点之间的通信？这样也可以实现不同主机上的容器ip不同且可以相互通信吧？**

答：还不支持指定哪个节点用那段IP，不过貌似可以在Etcd手改。

**问： Flannel只是负责通信服务，那是不是还要安装k8s？**
答：是的，k8s是单独的。

**问：现在Docker的网络组件还有什么可以选择或者推荐的?**

答：Overlay网络的常用就是Flannel和Weave，其他OVS之类的另说了。

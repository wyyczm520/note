# Scheduler预选策略

### NoDiskConflict算法

判断备选Pod的GCEPersistentDisk或AWSElasticBlockStore和备选的节点中已存在的Pod是否存在冲突。检测过程如下。
 
* 首先，读取备选Pod的所有Volume的信息（即pod.Spec.Volumes），对每个Volume执行以下步骤进行冲突检测。
* 如果Volume是GCEPersistentDisk，则将Volume和备选节点上的所有Pod的每个Volume进行比较，如果发现相同的GCEPersistentDisk，则返回false，表明存在磁盘冲突，检查结束，反馈给调度器该备选节点不适合作为备选Pod；如果该Volume是AWSElasticBlockStore，则将Volume和备选节点上的所有Pod的每个Volume进行比较，如果发现相同的AWSElasticBlockStore，则返回false，表明存在磁盘冲突，检查解锁，反馈调度器该备选节点不适合备选pod。
* 如果检查完毕备选pod的所有Volume均为发现冲突，则返回true，表明不存在磁盘冲突反馈个调度器该备选节点适合备选POD。

### PodFitsResources算法

判断备选节点的资源是否满足备选Pod的需求，检测过程如下。

* 计算备选pod和节点中已存在pod的所有容器的需求资源（内存和CPU）的总和。
* 获得备选节点的状态信息，其中包含节点的资源信息。
* 如果备选pod和节点中已存在pod的所有容器的需求资源（内存和CPU）总和，超出了备选节点拥有的资源，则返回false，表明备选节点不适合备选pod，否则返回true，表明备选节点适合备选POD。

### PodSelectorMatches算法

判断备选节点是否包含备选pod的标签选择器指定标签

* 如果pod没有指定spec.nodeSelector标签选择器，则返回true
* 否则，获得备选节点的标签信息，判断节点是否包含备选Pod的标签选择器（spec.nodeSelector）所指定的标签，如果包含返回true，否则返回false。

### PodFitsHost算法

判断备选Pod的spec.nodeName域锁指定的节点名称和备选节点的名称是否一致，如果一则返回true，否则返回false
CheckNodeLabelPresence算法

  ● 如果用户在配置文件中指定了该策略，则Scheduler会通过RegisterCustomFitPredicate方法注册该策略。该策略用于判断策略列出的标签在备选节点中存在时，是否选择该备选节点。
  ● 读取备选节点的标签列表信息
  ● 如果策略配置的标签列表存在于备选节点的标签列表中，且策略配置的presence值为false，则返回false,否则返回 true；如果策略配置的标签列表不存在备选节点的标签列表中，切策略配置的presence值为true，则返回false，否则返回true；

### CheckServiceAffinity算法

如果用户在配置文件中指定了该策略，则 Scheduler会通过RegisterCustomFitPredicate方法注册该策略。该策略用于判断备选节点是否包含策略指定的标签，或包含和备选Pod在相同 Service和Namespacex下的pod所在节点的标签列表。如果存在，则返回true，否则返回false。

### PodFitsPorts算法

判断备选Pod所用的端口列表中的端口是否在备选节点中是否已被占用，如果被占用，则返回false，否则返回true。

## Scheduler优选策略

### LeastRequestedPriority策略

该优选策略用于备选节点列表中选出资源消耗最小的节点。

  ● 计算出所有备选节点上运行的Pod和备选Pod的CPU占用量tatalMilliCPU
  ● 计算出所有备选节点上运行的pod和备选pod的内存占用量totalMemory.
  ● 计算每个节点得分，计算规则大致如下

NodeCpuCapacity为节点CPU计算能力：NodeMemoryCapacity为节点内存大小。
score=int ( ( ( nodeCpuCapacity-tatolMilliCPU)*10)/nodeCpuCapacity+((nodeMemoryCapacity-totalMemory)*10)/nodeCpuMemory)/2)
CalculateNodeLabelPriority策略

如果用户在配置文件中指定了该策略，则scheduler会通过RegisterCustomPriorityFunction方法注册该策略。该策略用于判断策略列出的标签在备选节点中存在时，是否选择该备选节点。如果备选节点的标签在优选策略的标签列表中切优选策略的presence值为true，或者备选节点的标签不在优选策略的标签列表中且优选策略presence值为false，则备选节点score=10，否则备选节点score=0

### BalanceResourceAllocation

该优选策略用于从备选节点列表中选出各项资源使用率最均衡的节点。

该优选策略用于从备选节点中选出各项资源使用率最均衡的节点。

计算出所有备选节点上运行的Pod和备选Pod的CPU占用量tatalMilliCPU

计算出所有备选节点上运行的Pod和备选Pod的内存占用量TatalMemory.

计算每个节点的得分，计算规则大致如下。

NodeCpuCapacity为节点CPU计算能力：NodeMemoryCapacity为节点内存大小
score = int(10-math.Abs(tatalMilliCPU/nodeCpuCapacity-totalMemory/nodeMemoryCapacity)*10)s


 

## pod模板属性说明

**属性名称：** version

**取值类型：** String

**是否必选：** Required

**取值说明：** v1

---

**属性名称：** kind

**取值类型：** String

**是否必选：** Required

**取值说明：** Pod

---

**属性名称：** metadata

**取值类型：** Object

**是否必选：** Required

**取值说明：** 元数据

---

**属性名称：** metadata.name

**取值类型：** String

**是否必选：** Required

**取值说明：** Pod名称，需要符合RFC1035规范

---

**属性名称：** metadata.namespace

**取值类型：** String

**是否必选：** Required

**取值说明：** 命名空间，不在指定系统是将使用名为"default"的命名空间

---

**属性名称：** metadata.labels[]

**取值类型：** List

**是否必选：** Required

**取值说明：** 自定义标签属性列表

---

**属性名称：** metadata.annotation[]

**取值类型：** List

**是否必选：** Required

**取值说明：** 自定义注释属性列表

---

**属性名称：** spec

**取值类型：** Object

**是否必选：** Required

**取值说明：** 详细描述

---

**属性名称：** spec.containers[]

**取值类型：** List

**是否必选：** Required

**取值说明：** Pod中运行的容器列表

---

**属性名称：** spec.containers[].name

**取值类型：** String

**是否必选：** Required

**取值说明：** 容器名称，需要符合RFC 1035规范

---

**属性名称：** spec.containers[].image

**取值类型：** String

**是否必选：** Required

**取值说明：** 容器镜像名称，在Node上如果不存在该镜像，则Kubelet会先下载

---

**属性名称：** spec.containers[].imagePullPolicy

**取值类型：** String

**取值说明：** 获取镜像的策略，可选值包括：Always , Never , IfNotPresent ,默认的为Always.
Always: 表示每次都下载镜像
IfNotPresent: 表示如果本地有该镜像，就是用本地镜像
Never: 表示使用本地镜像

---

**属性名称：** spec.containers[].command[]

**取值类型：** List

**取值说明：** 容器的启动命令列表，如果不指定，则使用镜像打包使用的CMD命令

---

**属性名称：** spec.containers[].workingDir

**取值类型：** String

**取值说明：** 容器的工作目录

---

**属性名称：** spec.containers[].volumeMounts[]

**取值类型：** List

**取值说明：** 可供容器使用的共享存储卷列表

---

**属性名称：** spec.containers[].volumeMounts[].name

**取值类型：** String

**取值说明：** 可引用POd定义的共享存储卷的名称，需使用volumes【】部分定义的共享存储卷名称

---

**属性名称：** spec.containers[].volumeMounts[].mountPath

**取值类型：** String

**取值说明：** 存储卷在容器内的Mount的绝对路径，应少于512个字符

---

**属性名称：** spec.containers[].volumeMounts[].readOnly

**取值类型：** boolean

**取值说明：** 是否为只读模式，默认为读写模式

---

**属性名称：** spec.containers[].ports[]

**取值类型：** List

**取值说明：** 容器需要暴露的端口列表

---

**属性名称：** spec.containers[].ports[].name

**取值类型：** String

**取值说明：** 端口名称

---

**属性名称：** spec.containers[].ports[].containerPort

**取值类型：** Int

**取值说明：** 容器需要暴露的端口列表

---

**属性名称：** spec.containers[].ports[].hostPort

**取值类型：** Int

**取值说明：** 容器所在主机需要监听的端口

---

**属性名称：** spec.containers[].ports[].protocol

**取值类型：** String

**取值说明：** 端口协议，支持TCP和UDP，默认为TCP

---

**属性名称：** spec.containers[].env[]

**取值类型：** List

**取值说明：** 容器运行前需要设置的环境变量

---

**属性名称：** spec.containers[].env[].name

**取值类型：** String

**取值说明：** 环境变量的名称

---

**属性名称：** spec.containers[].env[].value

**取值类型：** String

**取值说明：** 环境变量的值

---

**属性名称：** spec.containers[].resources

**取值类型：** Object

**取值说明：** 资源限制条件

---

**属性名称：** spec.containers[].resources.limits

**取值类型：** Object

**取值说明：** 资源限制条件

---

**属性名称：** spec.containers[].resource.limits.cpu

**取值类型：** Object

**取值说明：** CPU限制条件，将用于 docker run --cpu-share参数

---

**属性名称：** spec.containers[].resource.limits.memory

**取值类型：** String

**取值说明：** 内存限制条件，将用于dokcer run--memory参数

---

**属性名称：** spec.volumes[]

**取值类型：** List

**取值说明：** 在该Pod定义的共享存储卷列表

---

**属性名称：** spec.volumes[].name

**取值类型：** String

**取值说明：** 共享存储卷名称，需唯一，符合RPC 1035规范，容器定义部分containers[].volumeMounts[].name将应用该共享存储卷的名称

---

**属性名称：** spec.volumes[].emptyDir

**取值类型：** Object

**取值说明：** 默认的存储卷类型，表示与Pod同生命周期的一个临时某，其值为一个空对象,emptyDir:{}.该类型与hostPath类型互斥，应只定义一种

---

**属性名称：** spec.volumes[].hostPath

**取值类型：** Object

**取值说明：** 使用Pod所在主机的目录，通过volumes[],hostPath,path指定

---

**属性名称：** spec.volumes[].hostPath.path

**取值类型：** String

**取值说明：** Pod所在主机的目录，将被用于容器中mount的目录spec.dnsPolicyStringPod所在主机的目录，将被用于容器中mount的目录spec.restartPolicyObject该Pod内容器的重启策略，可选值为Always , OnFailure 默认值为Always
 Always:容器一旦终止运行，无论容器是如何终止的，Kubelet都将重启它。
 OnFailure:只有容器以非零退出码终止时，Kubelet才会重启该容器。如果容器正常结束（退出码为0），则Kubelet将不会重启它。
 Never：容器终止后，Kubelet将退出码报告给Master，不再重启它。
 
 ---
 
 **属性名称：** spec.nodeSelector
 
 **取值类型：** Object
 
 **取值说明：** 指定需要调度到Node的Label，以key=value格式指定
 
 ---
 
 **属性名称：** spec.imagePullSecrets
 
 **取值类型：** Object
 
 **取值说明：** Pull镜像时使用的secret名称，以name=secretkey格式定义。


Service的定义文件模板（yaml格式）

```
apiVersion: v1
kind: Service
metadata:
 name: string
 namespace: string
 labels:
 - name: string
 annotations:
 - name: string
spec:
 selector: []
 type: string
 clusterIP: string
 sessionAffinity: string
 ports:
 - name: string
 port: int
 targetPort: int
 protocol: string
 status:
 loadBalancer:
 ingress:
 ip: string
 hostname: string
```

对Service的定义文件模板的各属性的说明


属性名称取值类型是否必选取值说明

**属性名称：** version

**取值类型：** string

**是否必选：** Required

**取值说明：** v1

---

**属性名称：** kind

**取值类型：** string

**是否必选：** Required

**取值说明：** Service

---

**属性名称：** metadata

**取值类型：** object

**是否必选：** Required

**取值说明：** 元数据

---

**属性名称：** metadata.names

**取值类型：** string

**是否必选：** Required

**取值说明：** Service名称，虚符合RFC 1035规范

---

**属性名称：** metadata.namespace

**取值类型：** string

**是否必选：** Required

**取值说明：** 命名空间，不指定系统是将使用名为default的命名空间

---

**属性名称：** metadata.labels[]

**取值类型：** list

**取值说明：** 自定义标签属性列表

---

**属性名称：** metadata.annotation[]

**取值类型：** list

**取值说明：** 自定义纾解属性列表

---

**属性名称：** spec

**取值类型：** object

**是否必选：** Required

**取值说明：** 详细描述

---

**属性名称：** spec.selector[]

**取值类型：** list

**是否必选：** Required

**取值说明：** Label Selector配置，将选择具有指定Label标签的POD作为管理范围

---

**属性名称：** spec.type

**取值类型：** string

**是否必选：** Required

**取值说明：** Service的类型，指定Service的访问方式，默认为ClusterIP
ClusterIP：虚拟的服务器IP地址，该地址用于Kubernates集群内部的Pod访问，在Node上kube-proxy通过设置的iptables规则进行转发。
NodePort:使用宿主机的端口，是能够访问各Node的外部客户端通过Node的IP地址和端口号就能够访问服务。
LoadBalancer: 使用外接负载均衡器完成到服务的负载分发，需要在spec.status.loadBalancer字段指定外部负载均衡器的IP地址，并同时定义nodePort和clusterIP。

---

**属性名称：** spec.clusterIP

**取值类型：** string

**取值说明：** 虚拟服务IP地址，当type=ClusterIP时，如果不指定，则系统将自动分配；当type=LoadBalancer时，则需要指定

---

**属性名称：** spec.sessionAffinity

**取值类型：** string

**取值说明：** 是否支持Session，可选值为ClientIP，默认为空，ClientIP：标示将同一个客户端（根据客户端的IP地址决定）的访问请求都转发到同一个后端的pod

---

**属性名称：** spec.ports[]

**取值类型：** list

**取值说明：** Service需要暴露的端口号列表

---

**属性名称：** spec.ports[].name

**取值类型：** string

**取值说明：** 端口名称

---

**属性名称：** spec.ports[].port

**取值类型：** int

**取值说明：** 服务监听的端口号

---

**属性名称：** spec.ports[]targetPort

**取值类型：** int

**取值说明：** 需要转发到后端pod的端口号

---

**属性名称：** spec.ports[].protocol

**取值类型：** string

**取值说明：** 端口协议，支持TCP和UDP，默认为TCP

---

**属性名称：** Status

**取值类型：** object

**取值说明：** 当spec.type=LoadBalancer时，设置外部负载均衡器的地址

---

**属性名称：** status.loadBalancer

**取值类型：** object

**取值说明：** 外部负载均衡器

---

**属性名称：** status.loadBalancer.ingress

**取值类型：** object

**取值说明：** 外部均衡器

---

**属性名称：** status.loadBalancer.ingress.ip

**取值类型：** string

**取值说明：** 外部负载均衡器的IP地址status.loadBalancer.ingress.hostname

string外部负载均衡器的主机名

## Spring Cloud 简介

Spring Cloud是一个基于Spring Boot实现的微服务架构开发工具。它为微服务架构中设计的配置管理、服务治理、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等操作提供了一种简单的开发方式。

Spring Cloud包括了多个子项目（针对分布式系统中设计的多个不同开源产品，还可能会新增），如下所述

* Spring Cloud config : 配置管理工具，支持使用Git存储配置内容，可以使用它实现应用配置的外部化存储，并支持客户端配置信息刷新、加密/解密配置内容等。

* Spring Cloud Netflix ： 核心组件，对多个Netflix OSS开源套件进行整合。

  * Eureka : 服务治理组件，包含服务注册中心、服务注册与发现机制的实现。
  * Hystrix : 容错管理组件，实现断路器模式，帮助服务以来中出现的延迟和为故障提供强大的容错能力。
  * Ribbon : 客户端负载均衡的服务调用组件。
  * Feign : 基于Ribbon 和 Hystrix的声明式服务调用组件。
  * Zuul: 组件网关，提供智能路由、访问过滤等功能。
  * Archaius : 外部话配置组件。

* Spring Cloud Bus ： 事件、消息总线，用于传播集群中的状态变化或事件，以出发后续的处理，比如用来动态刷新配置等。

* Spring Cloud Cluster: 针对 Zookeeper 、 Redis 、 Hazelcast、Consul 的选举算法和通用状态模式实现。

* Spring Cloud Cloudfoundry : 与Pivotal Cloudfoundry 的整合支持。
* Spring Cloud Consul : 服务发现与配置管理工具。

* Spring Cloud Stream : 通过Redis 、 Rabbit 或者Kafka实现的消费服务，可以通过简单的声明模型来发送和接受消息。

* Spring Cloud AWS : 用于简化整合Amazon Web Service 的组件。

* Spring Cloud Security : 安全工具包，提供在Zuul代理中对OAuth2客户端请求的中继器。

* Spring Cloud Seleuth : Spring Cloud 应用的分布式跟踪实现，可以完美整合Zipkin。

* Spring Cloud Zookeeper : 基于Zookeeper的服务发现与配置管理组件。

* Spring Cloud Starters: Spring Cloud 的基础组件，它是基于Spring Boot风格项目的基础依赖模块。

* Spring Cloud CLI : 用于在Groovy中快速创建Spring Cloud 应用的Spring Boot CLI插件。

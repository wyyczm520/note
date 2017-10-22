# 第十四篇: 服务注册(consul)

这篇文章主要介绍 spring cloud consul 组件，它是一个提供服务发现和配置的工具。consul具有分布式、高可用、高扩展性。

### 一、consul 简介

consul 具有以下性质：

* 服务发现：consul通过http 方式注册服务，并且服务与服务之间相互感应。
* 服务健康监测
* key/value 存储
* 多数据中心
consul可运行在mac windows linux 等机器上。

### 二、consul安装

linux

```
$ mkdir -p $GOPATH/src/github.com/hashicorp && cd $!
$ git clone https://github.com/hashicorp/consul.git
$ cd consul
$ make bootstrap
$ make bootstrap
```

windows下安装：
见consul怎么在windows下安装

### 三、构建工程

构建一个consul-miya的springboot工程，导入依赖pring-cloud-starter-consul-discovery，其依赖文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.forezp</groupId>
    <artifactId>consul-miya</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>consul-miya</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Dalston.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>


</project>
```

在其入口文件ConsulMiyaApplication加入注解@EnableDiscoveryClient，开启服务发现：

```
@SpringBootApplication
@EnableDiscoveryClient
@RestController
public class ConsulMiyaApplication {

    @RequestMapping("/hi")
    public String home() {
        return "hi ,i'm miya";
    }

    public static void main(String[] args) {
        new SpringApplicationBuilder(ConsulMiyaApplication.class).web(true).run(args);
    }
}
```

在其配置文件application.yml指定consul服务的端口为8500：

```
spring:
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        healthCheckPath: ${management.contextPath}/health
        healthCheckInterval: 15s
        instance-id: consul-miya
  application:
    name: consul-miya
server:
  port: 8502
```

启动工程，访问 [http://localhost:8500](http://localhost:8500),可以发现consul-miya被注册了。

源码下载：

[https://github.com/forezp/SpringCloudLearning/tree/master/chapter14](https://github.com/forezp/SpringCloudLearning/tree/master/chapter14)

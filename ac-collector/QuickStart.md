# 快速入门

#### 接入监控

在实例中引入 collector-client-starter 以及 spring-boot-actuator.

此处所使用的DaoShop中`daoshop-product`服务源码在👉[Github](https://github.com/DaoCloud-Labs/daoshop-product)

spring-boot-1.5.x版本

```xml
<dependency>
    <groupId>io.daocloud.mircoservice</groupId>
    <artifactId>collector-client-starter-1x</artifactId>
    <version>2.0.0</version>
</dependency>
```

spring-boot-2.0以上版本

```xml
<dependency>
    <groupId>io.daocloud.mircoservice</groupId>
    <artifactId>collector-client-starter-2x</artifactId>
    <version>2.0.0</version>
</dependency>
```

actuator

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

同时，需要使用 eureka 获取 server 端地址，实例与 collector-server 需注册到同一个 eureka

collector-client-starter 的 maven 仓库为

```xml
<repository>
     <id>labs-releases</id>
     <name>labs-releases</name>
     <url>http://nexus.mschina.io/nexus/content/repositories/labs/</url>
</repository>
```



#### 开发环境中使用直连方式

对于开发环境中，无法通过从 eureka 得到的地址访问 collector-server 的情况，可通过直连的方式建立连接。这种情况中，请注意 collector-server 需要暴露特定的端口号，默认为 8889，可以通过配置文件进行修改。

**以下配置添加在需要被监控的实例中**

*collector-server:8889* 为 collector-server 的地址

```yaml
ms:
  collector:
    client:
      collect-urls: collector-server:8889
```

*2.0.0 版本的 starter 包中，需要实例与 collector-server 注册到相同的 eureka。*


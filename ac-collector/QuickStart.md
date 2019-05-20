# 接入说明

#### 一、引入依赖

在实例中引入 collector-client-starter 以及 spring-boot-actuator

此处所使用的DaoShop中`daoshop-product`服务源码在👉[Github](https://github.com/DaoCloud-Labs/daoshop-product)

spring-boot-1.5.x版本

```xml
<dependency>
    <groupId>io.daocloud.mircoservice</groupId>
    <artifactId>collector-client-starter-1x</artifactId>
    <version>2.0.1</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

spring-boot-2.0以上版本

```xml
<dependency>
    <groupId>io.daocloud.mircoservice</groupId>
    <artifactId>collector-client-starter-2x</artifactId>
    <version>2.0.1</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

collector-client-starter 的 maven 仓库为

```xml
<repository>
     <id>labs-releases</id>
     <name>labs-releases</name>
     <url>http://nexus.mschina.io/nexus/content/repositories/labs/</url>
</repository>
```



#### 二、注册 eureka

实例需要使用 eureka 实例列表，查看监控详情。同时，默认情况下，实例将通过 eureka 服务发现，寻找 collector-server 的地址以建立 websocket 连接，需要将实例与 collector-server 注册到同一 eureka。



#### 三、启动应用

应用启动后约 20s，得到如下日志，即为接入成功

```bash
2019-05-20 16:22:00.357  INFO 40886 --- [ector-ws-keeper] i.d.m.c.c.AbstractCollectorClientFactory : success connect to ws://172.16.100.88:8889
2019-05-20 16:22:00.384  INFO 40886 --- [ctReadThread-88] i.d.m.ccs.processor.DefaultProcessor     : welcome info received
```



#### 四、查看实例监控详情

接入成功的应用，可以通过实例监控列表查看监控详情，进入实例监控，蓝色可点击的实例表示已接入实例监控

![Ex3YqK.png](https://s2.ax1x.com/2019/05/20/Ex3YqK.png)

点击实例名称进入详情页面

![Ex35zn.png](https://s2.ax1x.com/2019/05/20/Ex35zn.png)





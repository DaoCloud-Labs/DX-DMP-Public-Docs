# 快速入门

在实例中引入 collector-client-starter 以及 spring-boot-actuator

spring-boot-1.5.x版本

```xml
<dependency>
    <groupId>io.daocloud.mircoservice</groupId>
    <artifactId>collector-client-starter-1x</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

spring-boot-2.0以上版本

```xml
<dependency>
    <groupId>io.daocloud.mircoservice</groupId>
    <artifactId>collector-client-starter-2x</artifactId>
    <version>1.0-SNAPSHOT</version>
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
    <id>labs-snapshots</id>
    <url>http://nexus.mschina.io/nexus/content/repositories/labs-snapshot/</url>
</repository>
```


# 引入依赖

本文主要讲解如何引入依赖，来接入Eureka的注册中心

主要依赖有：

```xml
		<!-- Eureka Client相关依赖 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 启用web服务 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

启动类代码如下：

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

在application.yml添加eureka-client相关配置，代码如下所示：

```
server:
  port: 8762
spring:
  application:
    name: eureka-client
eureka:
  instance:
    preferIpAddress: true
  client:
    serviceUrl:
      defaultZone: ${EUREKA_SERVER:http://localhost:8761/eureka/}
```

配置完成后启动应用即可

详情可见demo [github](https://github.com/DaoCloud-Labs/DMP-Demo/tree/master/eureka/eureka-client-demo)
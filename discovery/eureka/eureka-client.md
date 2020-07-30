# 使用 Eureka Client 获得注册服务

一些非 `Spring Boot` 类的 `JVM` 应用会选择直接使用 `Eureka Client` 获得服务的信息，此处的行为相较于 `Spring Cloud & Spring Boot` 需要自行与 `Http Client 客户端` 进行集成。

### Eureka Client

```java
EurekaClient eurekaClient = DiscoveryManager.getEurekaClient();
eurekaClient.getApplication("bookmark-service").getInstances((InstanceInfo instanceInfo)->{
    // 此处的 InstanceInfo 即真实的实例对象
    // 服务实例的端口号
    int port = instanceInfo.getPort();
    // 服务实例的地址 (Hostname/IP) 决定于注册上来的地址
    String address = instanceInfo.getIPAddr();
    System.out.println(ToStringBuilder.reflectionToString(s));
})
```

## 附录

1. [EurekaClient Example](https://github.com/Netflix/eureka/blob/master/eureka-examples/src/main/java/com/netflix/eureka/ExampleEurekaClient.java)

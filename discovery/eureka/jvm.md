# 常规 JVM 接入

## 引入依赖

在 `pom.xml` 中加入

```java
<dependency>
    <groupId>com.netflix.eureka</groupId>
    <artifactId>eureka-client</artifactId>
    <version>${eureka-version}</version>
</dependency>
```

## 初始化 Eureka Client

```java
// 使用配置进行 Eureka Client 初始化
DiscoveryManager.getInstance().initComponent(
                new CloudInstanceConfig(),
                new DefaultEurekaClientConfig());
```

## 创建配置文件

在`Resources`目录中创建 `eureka-client.properties` 配置文件，`Eureka Client` 在初始化的时候，默认读取此配置文件。

```yaml
eureka.name=${YOUR_Application_Name}
eureka.port=${YOUR_Application Port}
eureka.region=default
eureka.serviceUrl.default=${YOUR_EUREKA_ADDRESS}
```

相对于 `SpringCloud` 提供的 `Eureka Client` 来说，原生的 `Eureka Client` 没有提供 `eureka.instance.preferIpAddress` 这样的参数，会使用本机的 `Hostname` 注册到服务端，因此如果在公司内的网络无法通过 `Hostname` 访问，此方案是不行的。

## 附录

1. [Configuring Eureka](https://github.com/Netflix/eureka/wiki/Configuring-Eureka)

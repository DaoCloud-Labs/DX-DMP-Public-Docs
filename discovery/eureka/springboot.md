#  [推荐] SpringBoot & SpringCloud 接入

## 引入依赖

在 `pom.xml` 中加入

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

## 修改启动类（可选）

当应用内无冲突配置时，此步骤可忽略，随着 `依赖` 的增加，应用将自行启用 `eureka-client`

```java
@SpringBootApplication
@EnableDiscoveryClient
public class XXXApplication {

    public static void main(String[] args) {
        SpringApplication.run(XXXApplication.class, args);
    }
}
```

## 增加注册配置

在 `application.yml` 添加 `eureka-client` 相关配置，代码如下所示：

```yaml
eureka:
  instance:
    # 注意根据不同的版本此处配置可能是 prefer-ip-address: true , 下同
    preferIpAddress: true
    metadata-map:
    # 该配置为应用管理中应用的配置，可以通过 -Deureka.instance.metadata-map.dmp-application=DaoShop 在 Java Options 中传入。
      dmp-application: DaoShop
  client:
    serviceUrl:
      defaultZone: ${EUREKA_SERVER:http://localhost:8761/eureka/}
```

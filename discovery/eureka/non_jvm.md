# 非 JVM 应用接入

如果应用是 `非JVM` 的语言，我们可以采用 `Sidecar` 的方式将应用接入 `Eureka Server`。
![](http://cdn.jared-says.cn/eddc95fcgy1g0sx2c19waj20y80hoq36.jpg)

## 增加健康检查接口

因为 `Eureka Server` 需要对应用进行 `健康检查`，必须为代理的应用增加一个 `Restful` 的 `健康检查` 接口，此接口需要返回一个 `JSON` 对象，必须要包含 `status` 字段。

```json
{
  "status": "UP"
}
```

`status` 可设定为 2 个值 **UP**,**DOWN**。如果设置为其他的值，在 `Eureka Server` 上会显示为 `UNKOWN`，可参考<sup><a href='#附录' style="font-size:12px;">附录 2</a></sup>

## 新建 Sidecar 项目

1. 使用 [start.spring.io](https://start.spring.io/) 快速创建一个 `Spring Boot` 应用
2. 增加 `Sidecar` 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-netflix-sidecar</artifactId>
    <version>${NETFLIX_SIDECAR_VERSION}</version>
</dependency>
```

## 修改 Sidecar 项目配置

修改 `application.yml` 文件

```yaml
server:
  port: ${YOUR_SERVER_PORT}
spring:
  application:
    name: ${YOUR_SIDECARD_APPLICATION_NAME}
sidecar:
  # 代理应用的端口号
  port: 8000
  # 监控检查的地址: http://localhost:8000/health.json
  health-uri: ${YOUR_HEALTH_URI}
eureka:
  client:
    serviceUrl:
      defaultZone: ${YOUR_EUREKA_ADDRESS}
```

## 部署 Sidecar

`Sidecar` 仅支持与应用部署在同一层网络，也就是 `Sidecar` 仅仅支持通过 `localhost` 的方式对代理的应用进行访问。

- 虚拟机部署：`Sidecar` 与 `App` 部署在同一台虚拟机中
- 容器云平台部署：`Sidecar` 与 `App` 部署在同一个 `容器组(Pod)` 中

## 附录

1. [Polyglot support with Sidecar](https://cloud.spring.io/spring-cloud-netflix/multi/multi__polyglot_support_with_sidecar.html)
2. [Expose Health Check API](https://github.com/xetys/microservices-sidecar-example/blob/master/RailsApplication/app/controllers/application_controller.rb)

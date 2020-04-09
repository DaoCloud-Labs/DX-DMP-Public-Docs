# SpringBoot & SpringCloud 接入

## 引入依赖

在 `pom.xml` 中加入

```xml
    <dependency>
        <groupId>io.daocloud</groupId>
        <artifactId>discovery</artifactId>
        <version>2.3.1-SNAPSHOT</version>
    </dependency>
```

## 修改启动类（可选）

添加启动类注解

```java
@SpringBootApplication
@EnableDaocloudDiscovery
public class XXXApplication {

    public static void main(String[] args) {
        SpringApplication.run(XXXApplication.class, args);
    }
}
```

## 增加注册配置

在 `application.properties` 添加服务发现相关配置，代码如下所示：

```yaml
daocloud.discovery.debug=false
daocloud.discovery.ready-type=ready
daocloud.discovery.server=http://dx-brain
daocloud.discovery.refresh-service-interval=5000
```

## 相关参数含义
|参数|含义|说明
|:---:|:---:|:---:|
|daocloud.discovery.debug|本地debug模式|如果出现无法连接dcs_sail组件，可以本地模拟服务列表|
|daocloud.discovery.ready-type|服务可用的是标志|ready必须通过dce的ready检查，pod状态ready 之后才会被发现|
|daocloud.discovery.server|dx-brainl组件地址|dx-brain组件的地址维护服务列表，类似eureka-server。通过DCS部署时，可以不用配置该参数，DCS会自动将相关信息通过环境变量的形式导入|
|daocloud.discovery.refresh-service-interval|定时拉取服务列表的时间|以毫秒计算|

# 通过拦截器和MDC实现
该方法与上面不同的是，此方法通过拦截器实现，该场景适用于比如：想将每次请求的Ip继承到日志系统中，以及一些动态的值的集成。

此处讲解怎么实现前面通过`PatternLayout`实现的功能。
参考源码 ：[Github](https://git.mschina.io/microservice/demo/demo-project/tree/master/dmp-consumer)
## 思路
1. 做个全局拦截器，logback 的 MDC 类注入 appName，instanceId

2. logback 输出 pattern 标签里配置获取 MDC 注入的参数，如<pattern>[%X{appName}]  [%X{instanceId}]</pattern>

## 步骤：
1、继承`org.springframework.web.servlet.HandlerInterceptor`类：

在每次请求拦截的时候将需要写入日志的值通过MDC接口写入：

```java
@Component
@Slf4j
public class LogInterceptor implements HandlerInterceptor {

    private final static String REQUEST_ID = "requestId";

    @Value("${spring.application.name}")
    private String appName;

    @Value("${eureka.instance.instanceId}")
    private String instanceId;

    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        MDC.put("appName", appName);
        MDC.put("instanceId", instanceId);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        MDC.remove("appName");
        MDC.remove("instanceId");
    }

    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {

    }
}
```

2、注册该拦截器：

继承`org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter`类：

```java
@Configuration
public class WebMvcConfigurer extends WebMvcConfigurerAdapter {
    @Autowired
    private LogInterceptor logInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(logInterceptor);
        super.addInterceptors(registry);
    }
}
```

3、添加`logback.xml`配置

```xml
······
        <Console name="Console" target="SYSTEM_OUT" follow="true">
            <PatternLayout pattern="${LOG_PATTERN}"/>
        </Console>
······
```

4、启动程序查看日志输出：

```bash
2018-12-27 19:01:13.218 [dmp-consumer] [localhost:dmp-2018-12-27 19:01:10.835 [dmp-consumer] [localhost:dmp-consumer:1235] |-INFO  [http-nio-1235-exec-1] com.netflix.config.ChainedDynamicProperty [115] -| Flipping property: dmp-consumer.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647
2018-12-27 19:01:10.853 [dmp-consumer] [localhost:dmp-consumer:1235] |-INFO  [http-nio-1235-exec-1] com.netflix.util.concurrent.ShutdownEnabledTimer [58] -| Shutdown hook installed for: NFLoadBalancer-PingTimer-dmp-consumer
2018-12-27 19:01:10.862 [dmp-consumer] [localhost:dmp-consumer:1235] |-INFO  [http-nio-1235-exec-1] com.netflix.loadbalancer.BaseLoadBalancer [192] -| Client: dmp-consumer instantiated a LoadBalancer: DynamicServerListLoadBalancer:{NFLoadBalancer:name=dmp-consumer,current list of Servers=[],Load balancer stats=Zone stats: {},Server stats: []}ServerList:null
2018-12-27 19:01:10.871 [dmp-consumer] [localhost:dmp-consumer:1235] |-INFO  [http-nio-1235-exec-1] com.netflix.loadbalancer.DynamicServerListLoadBalancer [222] -| Using serverListUpdater PollingServerListUpdater
2018-12-27 19:01:10.877 [dmp-consumer] [localhost:dmp-consumer:1235] |-INFO  [http-nio-1235-exec-1] com.netflix.loadbalancer.DynamicServerListLoadBalancer [150] -| DynamicServerListLoadBalancer for client dmp-consumer initialized: DynamicServerListLoadBalancer:{NFLoadBalancer:name=dmp-consumer,current list of Servers=[],Load balancer stats=Zone stats: {},Server stats: []}ServerList:org.springframework.cloud.netflix.ribbon.eureka.DomainExtractingServerList@1e790ee6
2018-12-27 19:01:10.991 [dmp-consumer] [localhost:dmp-consumer:1235] |-ERROR [http-nio-1235-exec-1] org.apache.catalina.core.ContainerBase.[Tomcat].[localhost].
```
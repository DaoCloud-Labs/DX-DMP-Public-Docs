# 通过扩展PatternLayout的方式
在集成自定义的格式的日志的过程中，一般会采用过滤器或者拦截器的方式来做。然而，对于此处讲解的`将Spring Boot服务的AppName和Eureka的实例信息集成到日志系统中`的时候，应用名和实例Id在程序启动过后一般不会变化的，如果每次都去走过滤器和拦截器的话，感觉有点多余。

源码参考：[Github](https://git.mschina.io/microservice/demo/demo-project/tree/master/dmp-producer)
## 思路

- 在应用程序中添加对Spring Boot启动过程中`ApplicationEnvironmentPreparedEvent`事件的监听
，随后将AppName和InstanceId放入一个静态变量中。
- 继承`PatternLayout`之后，上面两个信息在日志输出过程中就会被填充上。

## 步骤：
1、创建自定义的格式转换器`AppInfoConverter`:
该类继承自`ch.qos.logback.classic.pattern.ClassicConverter`

```java
/**
 * @author jian.tan
 */
public class AppInfoConverter extends ClassicConverter {
    public static String appName;
    public static String instanceId;

    @Override public String convert(ILoggingEvent event) {
        return "App_Name: " + appName + "," + " Instance_Id: " + instanceId;
    }
}
```

2、将转换器添加到自定义的`PatternLayout`中：
该类继承自`ch.qos.logback.classic.PatternLayout`:

```java

import ch.qos.logback.classic.PatternLayout;

public class AppInfoPatternLayout extends PatternLayout {
    static {
        defaultConverterMap.put("app_info", AppInfoConverter.class.getName());
    }
}
```

3、添加对Spring Boot启动事件`ApplicationEnvironmentPreparedEvent `的监听:
- 继承`org.springframework.context.ApplicationListener`

```java
import org.springframework.boot.context.event.ApplicationEnvironmentPreparedEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.core.env.Environment;
import static com.example.webclientdemo.logback.AppInfoConverter.*;

public class ApplicationStartedEventListener implements ApplicationListener<ApplicationEnvironmentPreparedEvent> {

    @Override
    public void onApplicationEvent(ApplicationEnvironmentPreparedEvent event) {
        Environment environment = event.getEnvironment();
        appName = environment.getProperty("spring.application.name");
        instanceId = environment.getProperty("eureka.instance.instanceId");
    }
}
```

- 按照SpringBoot官方文档在`resources/META-INF`下面添加如下描述文件：

`spring.factories`

```text
org.springframework.context.ApplicationListener=com.example.webclientdemo.MyApplicationStartedEventListener
```

4、添加`logback.xml`配置文件
在`resources/logback.xml`下面添加如下内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <!--输出到控制台-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!-- <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
             <level>ERROR</level>
         </filter>-->
        <layout class="io.daocloud.apollodemo.logback.AppInfoPatternLayout">
            <pattern>%d{HH:mm:ss.SSS} %contextName [%app_info] [%thread] %-5level %logger{36} -%msg%n</pattern>
        </layout>
    </appender>

    <!--输出到文件-->
    <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 日志路径 可以是绝对路径也可以是相对路径-->
        <file>logs/logback-producer.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 定义了日志的切分方式 -->
            <fileNamePattern>logs/crn/apollo.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!-- 表示只保留最近30天的日志 -->
            <maxHistory>30</maxHistory>
            <!-- 用来指定日志文件的上限大小，例如设置为1GB的话，那么到了这个值，就会删除旧的日志。-->
            <totalSizeCap>1GB</totalSizeCap>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!-- maxFileSize:这是活动文件的大小，默认值是10MB -->
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %contextName [%app_info] [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    <!-- 用来指定最基础的日志输出级别，只有一个level属性。 -->
    <root level="info">
        <appender-ref ref="console"/>
        <appender-ref ref="file"/>
    </root>
</configuration>

```

5、最后启动程序查看效果：
发现`[App_Name: dmp-producer, Instance_Id: null]`等字样，其中`Instance_Id: null`是demo中没有接入Eureka的缘故。

```bash
18:13:34.415 default [App_Name: dmp-producer, Instance_Id: null] [main] INFO  i.d.apollodemo.ProducerApplication -No active profile set, falling back to default profiles: default
18:13:35.166 default [App_Name: dmp-producer, Instance_Id: null] [main] WARN  c.c.f.f.i.p.DefaultApplicationProvider -app.id is not available from System Property and /META-INF/app.properties. It is set to null
18:13:35.178 default [App_Name: dmp-producer, Instance_Id: null] [main] INFO  c.c.f.f.i.p.DefaultServerProvider -Environment is set to null. Because it is not available in either (1) JVM system property 'env', (2) OS env variable 'ENV' nor (3) property 'env' from the properties InputStream.
18:13:35.331 default [App_Name: dmp-producer, Instance_Id: null] [main] INFO  o.s.cloud.context.scope.GenericScope -BeanFactory id=7342d45a-9f38-3e75-b931-411c5d464b8f
```
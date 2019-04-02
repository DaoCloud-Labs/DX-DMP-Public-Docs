# 自定义忽略指定Trace Path

此方式作为一个可选插件来帮助我们忽略指定的Trace，比如常见的Eureka心跳信息等。

## 前置条件

获取到Skywalking探针的压缩包或者探针的下载地址(本文采用的方式)。
解压过后的目录结构如下：

```

+-- agent
    +-- activations
         apm-toolkit-log4j-1.x-activation.jar
         apm-toolkit-log4j-2.x-activation.jar
         apm-toolkit-logback-1.x-activation.jar
         ...
    +-- config
         agent.config  
    +-- plugins
         apm-dubbo-plugin.jar
         apm-feign-default-http-9.x.jar
         apm-httpClient-4.x-plugin.jar
         .....
    +-- optional-plugins
    		apm-spring-annotation-plugin.jar 
    		apm-trace-ignore-plugin.jar ➊
    skywalking-agent.jar
    
```

由于是可选插件，因此，默认情况下并没有激活，我们需要将➊中的jar包拷贝或剪切到上面的`plugins`目录下并添加相关配置来激活该插件,即：

```

+-- agent
    +-- activations
         apm-toolkit-log4j-1.x-activation.jar
         apm-toolkit-log4j-2.x-activation.jar
         apm-toolkit-logback-1.x-activation.jar
         ...
    +-- config
         agent.config  
    +-- plugins
         apm-dubbo-plugin.jar
         apm-feign-default-http-9.x.jar
         apm-httpClient-4.x-plugin.jar
         apm-trace-ignore-plugin.jar ➊ 注意此处所在文件夹为plugins。
         .....
    +-- optional-plugins
    		apm-spring-annotation-plugin.jar 
    skywalking-agent.jar
    
```

在`config`目录下创建`apm-trace-ignore-plugin.config`文件并添加如下配置激活插件：

```txt
trace.ignore_path=/your/path/1/**,/your/path/2/** ➊
```

➊ 中的路径配置规则支持`Ant Path`匹配风格，比如：
`/path/*`, `/path/**`, `/path/?`。匹配的Path路径将不会被记录。

## 最佳实践：Dockerfile编写

```dockerfile
FROM openjdk:8-jre-alpine

LABEL maintainer="jian.tan@daocloud.io"

ENV TZ=Asia/Shanghai \
    DIST_NAME=query-service \
    AGENT_REPO_URL="http://nexus.mschina.io/nexus/content/repositories/labs/org/apache/skywalking/dmp/agent/2.0.0/agent-2.0.0.gz" 

# Install required packages
RUN apk add --no-cache \
    bash \
    curl

ADD $AGENT_REPO_URL / 

RUN set -ex; \
    tar -zxf /agent-2.0.0.gz; \
    mv /skywalking-agent/optional-plugins/apm-trace-ignore-plugin-6.1.0-SNAPSHOT.jar /skywalking-agent/plugins/apm-trace-ignore-plugin-6.1.0-SNAPSHOT.jar; \ ➊
    echo 'trace.ignore_path=${TRACE_IGNORE_PATH:/eureka/apps/**}' >> /skywalking-agent/config/apm-trace-ignore-plugin.config; \ ➋
    rm -rf agent-2.0.0.gz;

RUN ln -sf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone

COPY target/"$DIST_NAME.jar" /"$DIST_NAME.jar"

EXPOSE 12801

ENTRYPOINT java -javaagent:/skywalking-agent/skywalking-agent.jar \
           -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap \
           $JAVA_OPTS -jar /$DIST_NAME.jar 
```

- ➊ 将trace-ignore插件拷贝至`plugins`目录下
- ➋ 添加相关配置

### 构建Docker镜像

```bash
docker build -t my-query-service-image .
```

### Docker 运行

```bash
docker run -e SW_AGENT_NAMESPACE=kfjiudjdif  \
-e SW_AGENT_NAME=query-service-demo  \
-e TRACE_IGNORE_PATH="/eureka/**" \ ➊
-e SW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.1:11800  \
-d my-query-service-image
```

- ➊ 通过环境变量覆盖需要忽略的path。

## 更多环境变量

 可以参考[探针参数配置](agent-settings.md)
 

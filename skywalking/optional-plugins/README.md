# 使用可选插件
插件包里面包含了一个`optional-plugins`目录，里面包含了一些在特定场景下不是必须的、可能会影响性能的插件。目录结构如下：

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
    		apm-lettuce-5.x-plugin.jar ➊
    skywalking-agent.jar
    
```

## 如何使用

这里以`Resis Client`的`Lettuce 5.x`插件为例。

使用起来非常简单，一句话——“将`optional-plugins`目录下的相关插件拷贝到`plugins目录即可` :D”.

### 结合Dockerfile使用


```dockerfile
FROM openjdk:8-jre-alpine

LABEL maintainer="jian.tan@daocloud.io"

ENV TZ=Asia/Shanghai \
    DIST_NAME=query-service \
    AGENT_REPO_URL="http://nexus.mschina.io/nexus/service/local/repositories/labs/content/io/daocloud/mircoservice/skywalking/agent/2.0.1/agent-2.0.1.gz" 

# Install required packages
RUN apk add --no-cache \
    bash \
    curl

ADD $AGENT_REPO_URL / 

RUN set -ex; \
    tar -zxf /agent-2.0.1.gz; \ 
    rm -rf agent-2.0.1.gz; \
    cp /skywalking-agent/optional-plugins/apm-lettuce-5.x-plugin-6.2.0-SNAPSHOT.jar /skywalking-agent/plugins/apm-lettuce-5.x-plugin-6.2.0-SNAPSHOT.jar; ➊


RUN ln -sf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone

COPY target/"$DIST_NAME.jar" /"$DIST_NAME.jar"

EXPOSE 12801

ENTRYPOINT java -javaagent:/skywalking-agent/skywalking-agent.jar \
           -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap \
           $JAVA_OPTS -jar /$DIST_NAME.jar 
```

- ➊ 将`lettuce-5.x`插件拷贝至`plugins`目录下




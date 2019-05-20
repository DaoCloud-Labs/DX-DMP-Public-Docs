# 构建通用探针镜像

## 通过Dockerfile将探针装入基础镜像中

```txt

FROM openjdk:8-jre-alpine

ENV TZ=Asia/Shanghai \
    AGENT_REPO_URL="http://nexus.mschina.io/nexus/service/local/repositories/labs/content/io/daocloud/mircoservice/skywalking/agent/2.0.1/agent-2.0.1.gz"

# Install required packages
RUN apk add --no-cache \
    bash

RUN set -ex; \
    ln -sf /usr/share/zoneinfo/$TZ  /etc/localtime; \
    echo $TZ > /etc/timezone;

ADD $AGENT_REPO_URL /

RUN set -ex; \
    tar -zxf /agent-2.0.1.gz; \
    rm -rf agent-2.0.1.gz;

```

## 构建镜像

比如:

```bash
docker build -t my-common-base-agent-image:latest .
```

## 如何使用
例如为Jar包方式部署的应用接入探针：

### 首先在打包Spring Boot/Cloud应用之前，需要修改`application.properties`或者`application.yml中`配置中：

```yml

spring:
  application:
    name: ${SW_AGENT_NAME:dmp-skywalking-query-service} 

```

此目的是为了使Spring Boot的服务名与skywalking中的服务名保持一致，统一从环境变量中取`SW_AGENT_NAME`变量的值。

### 然后，将Java应用中的Dockerfil使用的基础镜像改为上面构建出来的镜像名，比如:

```txt
FROM my-common-base-agent-image:latest #修改此处

······

ENTRYPOINT java -javaagent:/skywalking-agent/skywalking-agent.jar \ #此方式和前面Jar 包接入或容器接入介绍的一样
           -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap \
           $JAVA_OPTS -jar /$DIST_NAME.jar 

```

### 构建并运行镜像

和前面[通过容器接入](docker.md)中构建、运行容器方式一致：

##### 构建：

```bash
docker build -t my-query-service-image .
```

##### 运行：
```bash
docker run -e SW_AGENT_NAMESPACE=2 ➊ \
-e SW_AGENT_NAME=query-service-demo ➋ \
-e SW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.1:11800 ➌ \
-d my-query-service-image
```

- ➊ 配置探针`namespace`，该值与租户Skywalking OAP收集器的`namespace`保持一致，即租户Code。
- ➋ 设置该服务的服务名。
- ➌ 配置Skywalking OAP收集器的后端地址。

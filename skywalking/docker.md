# 基于Docker容器接入（最佳实践）

如果你的服务采用容器部署的话，可以参考该文档。本文档讲解了如何通过编写Dockerfile构建带有探针的应用镜像。另外，如果你需要提供一个内部通用的带有探针的基础镜像，可以参考[构建通用探针镜像](common-agent-image.md)。

## 前置条件
- 拿到Skywalking探针的压缩包或者探针的下载地址(本文采用的方式)。

## 修改`application.properties`或者`application.yml`配置文件:
为了使Spring Boot的服务名与Skywalking中的服务名保持一致，统一从环境变量中取`SW_AGENT_NAME`变量的值。
```yaml
spring:
  application:
    name: ${SW_AGENT_NAME:dmp-skywalking-query-service} 
```

## 将服务打包成Jar包

```bash
mvn clean package -U -DskipTests
```

假设jar包名字最终叫做`query-service.jar`。

## Dockerfile编写

```dockerfile
FROM openjdk:8-jre-alpine

LABEL maintainer="jian.tan@daocloud.io"

ENV TZ=Asia/Shanghai \
    DIST_NAME=query-service \
    AGENT_REPO_URL="http://nexus.mschina.io/nexus/content/repositories/labs/org/apache/skywalking/dmp/agent/2.0.0/agent-2.0.0.gz" ➊

# Install required packages
RUN apk add --no-cache \
    bash

ADD $AGENT_REPO_URL / ➋

RUN set -ex; \
    tar -zxf /agent-2.0.0.gz; \ ➌
    rm -rf agent-2.0.0.gz;

RUN ln -sf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone

COPY target/"$DIST_NAME.jar" /"$DIST_NAME.jar"

EXPOSE 12801

ENTRYPOINT java -javaagent:/skywalking-agent/skywalking-agent.jar ➍\
           -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap \
           $JAVA_OPTS -jar /$DIST_NAME.jar 
```

- ➊ ➋ 配置Skywalking探针的下载地址，并将其拷贝到`/`根目录。
- ➌ 解压探针。
- ➍ 使用`-javaagent`参数指定Skywalking探针的路径。

## 构建Docker镜像

```bash
docker build -t my-query-service-image .
```

## Docker 运行

```bash
docker run -e SW_AGENT_NAMESPACE=2 ➊ \
-e SW_AGENT_NAME=query-service-demo ➋ \
-e SW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.1:11800 ➌ \
-d my-query-service-image
```

- ➊ 配置探针`namespace`，该值与租户Skywalking OAP收集器的`namespace`保持一致，即租户Code。在有调用关系的多个应用中，若此值不一致将会导致调用链断链的情况。
- ➋ 设置该服务的服务名。
- ➌ 配置Skywalking OAP收集器的后端地址。

### 更多环境变量

 可以参考[探针参数配置](agent-settings.md), DaoShop `daoshop-product`服务中接入的[Dockerfile](https://github.com/DaoCloud-Labs/daoshop-product/blob/master/Dockerfile)、DaoShop `daoshop-order`服务中接入的[Dockerfile](https://github.com/DaoCloud-Labs/daoshop-order/blob/master/Dockerfile).
 

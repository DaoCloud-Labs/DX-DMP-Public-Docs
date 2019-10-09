# 构建通用探针镜像

## 通过Dockerfile将探针装入基础镜像中

```txt

FROM openjdk:8-jre-alpine

TODO:修改下载地址
ENV TZ=Asia/Shanghai \
    AGENT_REPO_URL="http://nexus.mschina.io/nexus/service/local/repositories/labs/content/io/daocloud/mircoservice/skywalking/agent/2.0.1/agent-2.0.1.gz"

RUN apk add curl
RUN curl $AGENT_REPO_URL > /vedfolnit-agent.jar

```

## 构建镜像

比如:

```bash
docker build -t my-common-base-agent-image:latest .
```

## 如何使用

有两种使用方式：

- 作为业务服务的基础镜像：例如在业务服务Dockerfile中采用此镜像`FROM my-common-base-agent-image:latest`。
- 通过Agent Sidecar方式使用,更多参考[基于容器Sidecar的方式接入](docker-sidecar.md)。
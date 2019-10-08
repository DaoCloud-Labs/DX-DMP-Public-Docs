# Jar包容器化使用

如果你想通过Docker容器来运行你的应用程序。你可以参考下面的方式来通过编写Dockerfile来制作镜像，并通过环境变量设置配置中心接入所需要的参数。

## 编写Dockerfile

前提条件是，你的将你的应用程序打包，比如： `mvn clean package -U -DskipTests`。
然后，我们将程序包打进镜像里面即可，假设jar包名字最终叫做`query-service.jar`。

- Dockerfile示例：

```dockerfile
FROM openjdk:8-jre-alpine

LABEL maintainer="jian.tan@daocloud.io"

ENV TZ=Asia/Shanghai \
    DIST_NAME=query-service

# Install required packages
RUN apk add --no-cache \
    bash

RUN ln -sf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone

COPY target/"$DIST_NAME.jar" /"$DIST_NAME.jar"

EXPOSE 12801

ENTRYPOINT -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap \
           $JAVA_OPTS -jar /$DIST_NAME.jar 
```

## 构建Docker镜像

```bash
docker build -t my-query-service-image .
```

## 运行容器

```bash
docker run -e APOLLO_CONFIGSERVICE=http://192.168.2.96:8080 ➊ \
-e APOLLO_APP_ID="zzzGEKiRSd.test11" ➋ \
-d my-query-service-image
```

- ➊ 配置中心Config Server的地址。
- ➋ 配置组ID

更多使用请参考：

* [通过注解方式开启](annotation.md)
* [通过配置文件开启](bootstrap.md)

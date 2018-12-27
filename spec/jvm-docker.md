# 让JVM感知Docker的参数
## 缘由
> Docker中的JVM检测到的是宿主机的内存信息，它无法感知容器的资源上限，这样可能会导致意外的情况。比如我们平时在启动容器是设置了容器资源，但是Java应用容器在运行中还是会莫名奇妙地被`OOM` Killer干掉。

### 问题的根源是什么？
- 对于JVM而言，如果没有设置Heap Size，就会按照宿主机环境的内存大小缺省设置自己的最大堆大小。
- Docker容器利用CGroup对进程使用的资源进行限制，而在容器中的JVM依然会利用宿主机环境的内存大小和CPU核数进行缺省设置，这导致了JVM
Heap的错误计算。

类似，JVM缺省的GC、JIT编译线程数量取决于宿主机CPU核数。如果我们在一个节点上运行多个Java应用，即使我们设置了CPU的限制，应用之间依然有可能因为GC线程抢占切换，导致应用性能受到影响。

了解了问题的根源，我们就可以非常简单地解决问题了

### 怎么解决？
**开启CGroup资源感知！！！**

与此同时，Java社区也关注到这个问题，并在JavaSE8u131+和JDK9 支持了对容器资源限制的自动感知能力。

在JDK 8u191和JDK 10之后，社区对JVM在容器中运行做了进一步的优化和增强。JVM可以自动感知Docker容器内部的CPU和内存资源限制。Java进程可用CPU核数由cpu sets, cpu shares 和 cpu quotas等参数计算而来。

相关文章可以参考👇：

- [Java SE support for Docker CPU and memory limits.](https://blogs.oracle.com/java-platform-group/java-se-support-for-docker-cpu-and-memory-limits)
- [Improved container integration 10](https://blog.docker.com/2018/04/improved-docker-container-integration-with-java-10/)

其具体用法就是添加如下参数，比如：
`java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -jar your-app.jar`

#### 那么，容器中怎么使用呢？
这里以常用的`jdk8`举例说明。当然，从上面的文章我们知道，`JavaSE8u131+`版本才支持这种方式。
所以我们选取`openjdk:8-jre-alpine`作为我们的基础镜像，**基本Dockerfile模版**：

```bash
FROM openjdk:8-jre-alpine

LABEL maintainer="hao.cheng@daocloud.io"

ENV DIST_NAME=demo-service

COPY target/"$DIST_NAME.jar" /"$DIST_NAME.jar"

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone

EXPOSE 8080

ENTRYPOINT java  -XX:+PrintFlagsFinal \
 -XX:+UnlockExperimentalVMOptions \
 -XX:+UseCGroupMemoryLimitForHeap \
 $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /$DIST_NAME.jar
 
```

## 总结
容器与虚拟机不同，其资源限制通过CGroup来实现。而容器内部进程如果不感知CGroup的限制，就进行内存、CPU分配可能导致资源冲突和问题。
如果您无法升级您的Java版本，请使用`-Xmx`设置您自己的限制。



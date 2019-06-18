# JVM OOM文件储存方案

OOM-kill就是out-of-memory，在linux内核中有一层保护机制，用于避免linux在内存不足的时候不至于严重的问题，把无关紧要的进程杀掉。

然而，有的时候我们的应用程序被OOM killer杀掉之后我们需要进行问题的排查。我们可以通过分析dump日志来定位分析OOM异常。

## 怎么抓取Dump文件？

通过设置JVM参数实现。

### 相关参数

- `-XX:+HeapDumpOnOutOfMemoryError`

	设置当首次遭遇内存溢出时导出此时堆中相关信息。

- `-XX:HeapDumpPath`

	指定导出堆信息时的路径或文件名，比如：`-XX:HeapDumpPath=/tmp/heapdump.hprof`
	
	
### 具体使用

- 通过Java命令启动时设置 Jvm 参数：

```bash
java -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions \
	-XX:+UseCGroupMemoryLimitForHeap \
	-XX:+HeapDumpOnOutOfMemoryError \
	-XX:HeapDumpPath=/tmp/heapdump.hprof -jar app.jar
```

- 如果使用容器部署Java应用:

```bash
docker run \
-e JAVA_OPTS="... -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/heapdump.hprof..." \
......
```

- 另外，还要规定好容器平台从容器中采集`/tmp/heapdump.hprof`文件。

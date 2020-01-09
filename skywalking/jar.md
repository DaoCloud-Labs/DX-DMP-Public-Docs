# Jar包接入

### 下载探针

`https://nexus.daocloud.io/repository/dx-public/io/daocloud/mircoservice/skywalking/agent/2.1.4/agent-2.1.4.gz`

### 解压缩探针安装包
解压后，你将获得如下的目录结构：

```bash
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
    skywalking-agent.jar
```

### 修改配置文件
打开`config/agent.config`配置文件，首先我们只看最主要的两个配置：`agent.service_name`和`collector.backend_service`:

```bash
# The service name in UI
agent.service_name=${SW_AGENT_NAME:Your_ApplicationName}

······

# Backend service addresses.
collector.backend_service=${SW_AGENT_COLLECTOR_BACKEND_SERVICES:127.0.0.1:11800}

······
```
- `agent.service_name`:
此配置是标记你的服务在UI界面上展示的名称，需要和`Spring Boot/Cloud`中的`spring.application.name`的值保持一致。
- `collector.backend_service`: 此配置为后端收集器的地址，比如`127.0.0.1:11800`。

更多的配置可以参考👉[参数配置](agent-settings.md)

### 启动Jar包
此步骤比较简单。只需要在你服务启动参数中加上`-javaagent:/path/to/skywalking-package/agenxt/skywalking-agent.jar`即可。其中，参数中的`skywalking-agent.jar`为你上面步骤中解压的具体路径，比如: `~/Desktop/skywalking-agent/skywalking-agent.jar`。

```bash
java -javaagent:/path/to/skywalking-agent/skywalking-agent.jar -jar yourAppDemo.jar
```

当然，你也可以在此命令中传参覆盖`config/agent.config`中的配置，比如覆盖`agent.service_name`和`collector.backend_service`。需要注意的是，如果通过这种Vm Options的方式覆盖配置的话需要加上`skywalking`前缀，如下：

- 通过`-D`进行覆盖

```bash
java -javaagent:/path/to/skywalking-agent/skywalking-agent.jar -jar -Dskywalking.collector.servers=127.0.0.2:11800 -Dskywalking.agent.service_name=Test-Demo yourAppDemo.jar
```
- 通过环境变量进行覆盖(推荐)
在`config/agent.config`中，我们看到了如下的内容：

```bash
agent.service_name=${SW_AGENT_NAME:Your_ApplicationName}
```

其中每行结尾处通过`${...}`进行包裹的变量就是我们可以通过环境变量来进行覆盖配置的关键。比如覆盖`agent.service_name`和`collector.backend_service`：

```bash
SW_AGENT_NAME=Test-Demo-Agent \
SW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.3:11800 \
java -javaagent:/path/to/skywalking-agent/skywalking-agent.jar -jar yourAppDemo.jar
```

更多的配置可以参考👉[参数配置](agent-settings.md)


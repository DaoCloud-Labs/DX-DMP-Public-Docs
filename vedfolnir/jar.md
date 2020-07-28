## Jar 包接入

##### 下载探针

//TODO 添加release jar包地址

##### 启动 Jar 包

首先传入所必须的环境变量参数(参考👉[配置参数说明](agent-settings.md))，然后使用 -javaagent 传入上一步骤下载的 jar 包，启动应用即可
```bash
VEDFOLNIR_SERVER_URL=127.0.0.1:8002 \
AGENT_INSTANCE_ID=test-instance \
DMP_ENVIRONMENT_CODE=1 \
···
java -javaagent:/path/to/vedfolnir-agent.jar -jar yourAppDemo.jar
```
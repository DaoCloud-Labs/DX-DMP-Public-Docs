# 接入说明

#### 配置参数说明
|  环境变量名   | 默认值  |说明  | 是否必须  |
|  ----  | ----  |---- |----  |
| VEDFOLNIR_WS_DELAY  | 10 s | 探针启动后，与 vedfolnir-manager 连接初始化延迟  |否  |
| VEDFOLNIR_PROMETHEUS_PORT | 8888 | metrics 端口号  | 否  |
| VEDFOLNIR_IS_EXPOSE_PROMETHEUS  | false | 是否需要暴露 metrics 接口  | 否  |
| VEDFOLNIR_SERVER_URL  |  | vedfolnir-manager url | 是  |
| AGENT_INSTANCE_ID  |  | 实例 id  | 是  |
| AGENT_NAME  |  | 当前实例服务名  | 是  |
| DMP_ENVIRONMENT_CODE  |  | 当前实例所在环境 Code  | 是  |


#### 一、Jar 包接入

##### 下载 Jar 包

//TODO 添加release jar包地址

##### 启动 Jar 包

首先传入所必须的环境变量，使用 -javaagent 传入上一步骤下载的 jar 包，启动应用即可
```bash
VEDFOLNIR_SERVER_URL=127.0.0.1:8002 \
INSTANCE_ID=test-instance \
DMP_ENVIRONMENT_CODE=1 \
···
java -javaagent:/path/to/vedfolnir-agent.jar -jar yourAppDemo.jar
```

#### 二、War 包接入

接入 War 包的方式和 Jar 包接入方式大致相同，只是添加 -javaagent 参数位置不同，需要修改 Tomcat 中的`CATALINA_OPTS`变量。

##### 修改Tomcat中CATALINA_OPTS变量
在Linux Tomcat 7, Tomcat 8环境下： 修改tomcat/bin/catalina.sh的第一行：
```bash
CATALINA_OPTS="$CATALINA_OPTS -javaagent:/path/to/vedfolnir-agent.jar"; export CATALINA_OPTS
```
在Windows Tomcat 7, Tomcat 8环境下： 修改tomcat/bin/catalina.bat的第一行：
```bash
set "CATALINA_OPTS=-javaagent:/path/to/vedfolnir-agent.jar"
```



#### 三、Sidecar 接入

##### 添加环境变量
首先需要将待接入应用容器化，Dockfile 中，需添加环境变量 AGENT_OPTS，可参考以下代码

```bash
ENTRYPOINT java $AGENT_OPTS \
           -XX:+PrintFlagsFinal -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap $JAVA_OPTS -jar admin-0.0.1-SNAPSHOT.jar
```
##### 修改编排文件
在编排文件中添加初始化容器，以及文件挂载的步骤，使应用可以共享 jar 包资源，可参考以下代码

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: dmp-dev
  name: ns-daoshop-admin
  labels:
    app: ns-daoshop-admin
spec:
  selector:
    matchLabels:
      app: ns-daoshop-admin
  template:
    metadata:
      labels:
        app: ns-daoshop-admin
    spec:
      imagePullSecrets:
      - name: registry-pull
      initContainers:
      - name: agent-sidecar
        image: registry.dx.io/daocloud-dmp/dx-vedfolnir-agent-sidecar:latest # 容器镜像，包含静态资源文件
        command: ['sh', '-c', 'cp -r /vedfolnir /sidecar/'] # 拷贝 vedfolnit-jar 到 /sidecar/vedfolnir/ 目录
        volumeMounts:
        - name: agent-sidecar
          mountPath: /sidecar
      containers:
      - image: {{ ns-daoshop-admin.image }}
        name: ns-daoshop-admin
        resources:
          requests:
            memory: "1000Mi"
            cpu: "500m"
          limits:
            memory: "1000Mi"
            cpu: "500m"
        ports:
        - containerPort: 18083
        env:
        - name: VEDFOLNIR_SERVER_URL
          value: 'ws://dmp-vedfolnir-manager.dmp-dev:8002'
        ···
        - name: AGENT_OPTS
          value: "-javaagent:/sidecar/vedfolnir/vedfolnir-agent.jar" #jar 包启动参数
        volumeMounts:
        - name: host-time
          mountPath: /etc/localtime
          readOnly: true
        - name: agent-sidecar
          mountPath: /sidecar
      volumes:
      - name: host-time
        hostPath:
          path: /etc/localtime
      - name: agent-sidecar  #共享agent文件夹
        emptyDir: {}
      restartPolicy: Always
---
apiVersion: v1
kind: Service
metadata:
  namespace: dmp-dev
  name: ns-daoshop-admin
spec:
  type: NodePort
  ports:
  - port: 18083
    nodePort: 30083
  selector:
    app: ns-daoshop-admin
```




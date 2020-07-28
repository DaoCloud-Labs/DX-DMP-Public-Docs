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

使用起来非常简单：“将`optional-plugins`目录下的相关插件拷贝到`plugins目录即可`”😄.

## 在初始化容器中添加 CMD 进行操作

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: dmp-ns1
  name: daoshop-order
  labels:
    app: daoshop-order
    dx.daocloud.io/trace-agent: "true"
    dx.daocloud.io/monitor-agent: "true"
spec:
  selector:
    matchLabels:
      app: daoshop-order
  template:
    metadata:
      labels:
        app: daoshop-order
        dx.daocloud.io/trace-agent: "true"
        dx.daocloud.io/monitor-agent: "true"
    spec:
      # refs: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/
      initContainers:
        - name: dx-monitor-agent-sidecar
          image: registry.dx.io/dx-pilot/dx-monitor-agent-sidecar:2.4.0-3cdaa67
          imagePullPolicy: Always
          command: 
            - "sh"
            - "-c"
            - > 
              mv /sidecar/skywalking/agent/optional-plugins/apm-trace-ignore-plugin-6.6.0-SNAPSHOT.jar /sidecar/skywalking/agent/plugins;
              echo 'trace.ignore_path=${TRACE_IGNORE_PATH:/api/sail/**,/metrics/**,/resource/v0.1/**}' >> /sidecar/skywalking/agent/config/apm-trace-ignore-plugin.config;
              mv /sidecar/skywalking/agent/optional-plugins/apm-lettuce-5.x-plugin.jar /sidecar/skywalking/agent/plugins;   ➊
              cp -r /sidecar /target;
          volumeMounts:
            - name: sidecar
              mountPath: /target
      containers:
        - image: {{ daoshop-order.image }}
          name: daoshop-order
          resources:
            requests:
              memory: "2048Mi"
              cpu: "500m"
            limits:
              memory: "2048Mi"
              cpu: "500m"
          ports:
            - containerPort: 8080
              name: web
            - containerPort: 8888
              name: metrics
          env:
            - name: JAVA_OPTS
              value: "-javaagent:/sidecar/sidecar/skywalking/agent/skywalking-agent.jar -javaagent:/sidecar/sidecar/vedfolnir/vedfolnir-agent.jar"
            - name: DX_APP_NAME
              value: "test-app"
            - name: DX_ENV_ID
              value: "test"
            - name: DX_DMP_TRACING_SERVER
              value: dmp-skywalking-oap-ng.dmp-2-4-x.svc:11800
            - name: DX_DMP_ACTUATOR_SERVER
              value: ws://dx-fate:9056
          volumeMounts:
            - name: sidecar
              mountPath: /sidecar
      volumes:
        - name: sidecar  #共享agent文件夹
          emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: daoshop-order
spec:
  type: NodePort
  ports:
    - port: 8080
      name: web
    - port: 8888
      name: metrics
  selector:
    app: daoshop-order

```

- ➊ 将`lettuce-5.x`插件拷贝至`plugins`目录下




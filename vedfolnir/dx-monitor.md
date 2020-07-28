## 对接 DX-监控告警

### 步骤

对接告警平台，要求应用主动暴露 metrics 接口。

- 首先在配置文件中，设置环境变量 `VEDFOLNIR_IS_EXPOSE_PROMETHEUS` 为 `true`(默认为 true，端口号 8888)，如需指定端口，可以设置 `VEDFOLNIR_PROMETHEUS_PORT`为特定端口号。

- 部署应用时，yaml 文档中开放该端口

### 编排文件参考

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: dmp-ns1
  name: daoshop-order
  labels:
    app: daoshop-order
spec:
  selector:
    matchLabels:
      app: daoshop-order
  template:
    metadata:
      labels:
        app: daoshop-order
    spec:
      # refs: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/
      initContainers:
        - name: dx-monitor-agent-sidecar
          image: registry.dx.io/daocloud-dmp/dx-monitor-agent-sidecar:release-2.4.0
          imagePullPolicy: IfNotPresent
          command: 
            - "sh"
            - "-c"
            - > 
              mv /sidecar/skywalking/agent/optional-plugins/apm-trace-ignore-plugin-6.5.0-SNAPSHOT.jar /sidecar/skywalking/agent/plugins;
              echo 'trace.ignore_path=${TRACE_IGNORE_PATH:/api/sail/**,/metrics/**}' >> /sidecar/skywalking/agent/config/apm-trace-ignore-plugin.config;
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
            - containerPort: 8888 ➊
              name: metrics
          env:
            - name: JAVA_OPTS
              value: "-javaagent:/sidecar/sidecar/skywalking/agent/skywalking-agent.jar -javaagent:/sidecar/sidecar/vedfolnir/vedfolnir-agent.jar"
           ··· #此处省略多个环境变量
            - name: VEDFOLNIR_IS_EXPOSE_PROMETHEUS
              value: "true"       #默认值为true，如果使用默认值，可省略该配置
            - name: VEDFOLNIR_PROMETHEUS_PORT
              value: "8888"       #默认值为8888，如果使用默认值，可省略该配置
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
    - port: 8888 ➋
      name: metrics
  selector:
    app: daoshop-order
```

➊、➋ 在编排文件中暴露监控端口

相关环境变量请参考👉[配置参数说明](agent-settings.md)
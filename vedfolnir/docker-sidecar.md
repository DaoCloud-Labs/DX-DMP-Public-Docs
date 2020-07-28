## 基于容器Sidecar的方式接入

如果你的服务采用容器部署的话，可以参考该文档。本文档讲解了如何通过 kubernetes 部署YAML文件使用 Sidecar 的方式接入应用监控。本文采用 DaoCloud 发布的 Sidecar 镜像包为例。

## 前置条件

- 能够拉取/下载 DaoCloud 发布的应用监控 Agent Sidecar 镜像。

## 步骤

### 拉取镜像

### 编排文件参考

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
        image: registry.dx.io/dx-pilot/dx-monitor-agent-sidecar:2.4.0
        command: ["cp", "-r", "/sidecar", "/target"]  ➊
        volumeMounts:
        - name: sidecar
          mountPath: /target
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
        - name: JAVA_OPTS
          value: "-javaagent:/sidecar/sidecar/vedfolnir/vedfolnir-agent.jar" ➋
        volumeMounts:
        - name: host-time
          mountPath: /etc/localtime
          readOnly: true
        - name: sidecar
          mountPath: /sidecar
      volumes:
      - name: host-time
        hostPath:
          path: /etc/localtime
      - name: sidecar  #共享agent文件夹
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

➊ 将带有Agent的镜像中的探针拷贝到共享目录。

➋ 使用-javaagent参数指定Vedfolnir探针的路径

相关环境变量请参考👉[配置参数说明](agent-settings.md)




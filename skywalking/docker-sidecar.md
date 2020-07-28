# 基于容器Sidecar的方式接入（最佳实践）

如果你的服务采用容器部署的话，可以参考该文档。本文档讲解了如何通过`daoshop-user-center`的 kubernetes 部署 YAML 文件讲解 Sidecar 的方式接入分布式链路追踪。本文采用 DaoCloud 发布的 Sidecar 镜像包为例。

## 前置条件

- 能够拉取/下载 DaoCloud 发布的链路追踪 Agent Sidecar 镜像。

## 步骤

#### 拉取镜像

### 编排文件参考

主要利用了 Kubernetes 的 initContainer 机制，更多请参考: [https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/)

```yml

apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: dmp-ns1
  name: daoshop-user-center
  labels:
    app: daoshop-user-center
spec:
  selector:
    matchLabels:
      app: daoshop-user-center
  template:
    metadata:
      labels:
        app: daoshop-user-center
    spec:
      # refs: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/
      initContainers:
        - name: dx-monitor-agent-sidecar
          image: registry.dx.io/dx-pilot/dx-monitor-agent-sidecar:2.4.0-3cdaa67
          imagePullPolicy: IfNotPresent
          command: ["cp", "-r", "/sidecar", "/target"] ➊
          volumeMounts:
            - name: sidecar
              mountPath: /target
      containers:
        - image: {{ daoshop-user-center.image }}
          name: daoshop-user-center
          resources:
            requests:
              memory: "2048Mi"
              cpu: "500m"
            limits:
              memory: "2048Mi"
              cpu: "500m"
          ports:
            - containerPort: 18081
          env:
            - name: JAVA_OPTS
              value: "-javaagent:/sidecar/sidecar/skywalking/agent/skywalking-agent.jar" ➋
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
  name: daoshop-user-center
spec:
  type: NodePort
  ports:
    - port: 18081
  selector:
    app: daoshop-user-center

```

- ➊ 将带有Agent的镜像中的探针拷贝到共享目录。
- ➋ 使用`-javaagent`参数指定 Skywalking 探针的路径。

注意⚠️：探针所需相关环境变量均会通过DX部署的时候传入容器。

### 更多环境变量

 可以参考[探针参数配置](agent-settings.md), DaoShop `daoshop-product`服务中接入的[Dockerfile](https://github.com/DaoCloud-Labs/daoshop-product/blob/master/Dockerfile)、DaoShop `daoshop-order`服务中接入的[Dockerfile](https://github.com/DaoCloud-Labs/daoshop-order/blob/master/Dockerfile).
 

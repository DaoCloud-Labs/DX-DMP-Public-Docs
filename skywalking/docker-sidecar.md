# 基于容器Sidecar的方式接入（最佳实践）

如果你的服务采用容器部署的话，可以参考该文档。本文档讲解了如何通过`daoshop-user-center`的kubernetes 部署YAML文件讲解Sidecar的方式接入分布式链路追踪。本文采用DaoCloud发布的Sidecar镜像包为例。另外，如果你需要提供一个内部通用的带有探针的基础镜像，可以参考[构建通用探针镜像](common-agent-image.md)自己构建Sidecar镜像。

## 前置条件

- 能够拉取/下载DaoCloud发布的链路追踪Agent Sidecar镜像。
- 通过DX-Pilot中DCS部署。

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
          image: registry.dx.io/daocloud-dmp/dx-monitor-agent-sidecar:release-2.3.0-0b0cbd1
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
            - name: AGENT_NAME  ➌
              valueFrom:
                fieldRef:
                  fieldPath: 'metadata.labels[''dx.daocloud.io/service-name'']'
            - name: AGENT_INSTANCE_UUID
              valueFrom:
                fieldRef:
                  fieldPath: 'metadata.name'
            - name: AGENT_INSTANCE_ID
              valueFrom:
                fieldRef:
                  fieldPath: 'metadata.name'
            - name: DMP_ENVIRONMENT_CODE
              valueFrom:
                fieldRef:
                  fieldPath: 'metadata.labels[''dx.daocloud.io/env-id'']'
            - name: DX_APPLICATION_NAME
              valueFrom:
                fieldRef:
                  fieldPath: 'metadata.labels[''dx.daocloud.io/application-name'']'
            - name: DX_APPLICATION_ID
              valueFrom:
                fieldRef:
                  fieldPath: 'metadata.labels[''dx.daocloud.io/application-id'']'
            - name: SW_AGENT_COLLECTOR_BACKEND_SERVICES  ➍
              value: dx-skywalking-oap-ng.dx-pilot.svc:11800
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
- ➋ 使用`-javaagent`参数指定Skywalking探针的路径
- ➌ ➍ 配置相关环境变量

### 更多环境变量

TODO：更新
 可以参考[探针参数配置](agent-settings.md), DaoShop `daoshop-product`服务中接入的[Dockerfile](https://github.com/DaoCloud-Labs/daoshop-product/blob/master/Dockerfile)、DaoShop `daoshop-order`服务中接入的[Dockerfile](https://github.com/DaoCloud-Labs/daoshop-order/blob/master/Dockerfile).
 

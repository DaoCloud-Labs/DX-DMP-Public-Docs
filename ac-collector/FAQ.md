#### 应用启动后报错 can't get a websocket

在采集端，默认情况下，应用会从 eureka 获取 collector-server 的地址，并尝试与该地址建立 websocket 连接。因此，该问题可以从以下几个步骤排查：

- 确保应用与 collector-server 已经注册到同一 eureka 上。
- 检查网络是否畅通。由于 collector-server 通常部署在容器中，可能存在网络不通的情况。

如果是由于网络不通导致连接不上，可以采取直连的方式，参考👉[开发环境中使用直连模式](https://daocloud-labs.github.io/DMP-Public-Docs/ac-collector/AdvancedFeatures.html)


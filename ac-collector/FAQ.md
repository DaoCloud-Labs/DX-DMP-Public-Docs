#### 一、拉取不到 collector-client 系列 jar 包

- 确认 pom.xml 中是否配置正确的 repository 地址，并且有该仓库的访问权限。

- 查看 .m2 文件夹中，settings.xml 文件中是否有下面这种类型的仓库配置，<mirrorOf>*</mirrorOf> 表示让该仓库镜像所有的仓库请求，会拦截所有拉取构件的请求，转到该仓库，如果该仓库没有需要的构件，则拉取失败

  ```xml
  <mirror>
       <id>internal-repository</id>
       <name>Maven Repository Manager running on repo.mycompany.com</name>
       <url>http://repo.mycompany.com/proxy</url>
       <mirrorOf>*</mirrorOf>
  </mirror>
  ```

- 复杂情况可以查看详细拉取日志，根据具体情况调整

  ```bash
  mvn clean package -P -U -Dmaven.test.skip=true 
  ```
  
  
#### 二、应用启动后报错 can't get a websocket

在采集端，默认情况下，应用会从 eureka 获取 collector-server 的地址，并尝试与该地址建立 websocket 连接。因此，该问题可以从以下几个步骤排查：

- 确保应用与 collector-server 已经注册到同一 eureka 上。
- 检查网络是否畅通。由于 collector-server 通常部署在容器中，可能存在网络不通的情况。

如果是由于网络不通导致连接不上，可以采取直连的方式，参考👉[开发环境中使用直连模式](https://daocloud-labs.github.io/DMP-Public-Docs/ac-collector/AdvancedFeatures.html)
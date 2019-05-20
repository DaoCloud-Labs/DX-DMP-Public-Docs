# 高级功能

#### 开发环境中使用直连方式

对于开发环境中，无法通过从 eureka 得到的地址访问 collector-server 的情况，可通过直连的方式建立连接。这种情况中，请注意 collector-server 需要暴露特定的端口号，默认为 8889，可以通过配置文件进行修改。

**以下配置添加在需要被监控的实例中**

*collector-server:8889* 为 collector-server 的地址

```yaml
ms:
  collector:
    client:
      collect-urls: collector-server:8889
```

#### 支持 prometheus 

Collector-client 2.0.1 系列 jar 包，增加对 prometheus 的支持，暴露 http 接口提供指标查询。默认端口为 8888，路径为 /prometheus。

可通过配置文件修改端口号与访问路径，如下

```yaml
ms:
  collector:
    client:
      management-server-path: /metrics
      management-server-port: 8887
```


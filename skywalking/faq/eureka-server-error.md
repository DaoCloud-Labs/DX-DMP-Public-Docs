# 为什么服务加上探针就注册不了Eureka Server?

首先确定是否和如下现象一致。

## 现象

服务不加探针之前能够正常注册到 Eureka Server，加上探针之后就注册不了并伴随如下错误信息：

```bash
······
[2019-05-28 15:53:19] [WARN] - com.netflix.discovery.shared.transport.decorator.RetryableEurekaHttpClient -Request execution failure with status code 400; retrying on another server if available
[2019-05-28 15:53:19] [WARN] - com.netflix.discovery.DiscoveryClient -DiscoveryClient_TEST-DEMO/MSGAPPDEV01V:test-demo:9090 - registration failed Cannot execute request on any known server
······
[2019-05-28 15:53:48] [ERROR] - com.sun.jersey.api.client.ClientResponse -The registered message body readers compatible with the MIME media type are:
*/* ->
  com.sun.jersey.core.impl.provider.entity.FormProvider
  com.sun.jersey.core.impl.provider.entity.StringProvider
  com.sun.jersey.core.impl.provider.entity.ByteArrayProvider
  com.sun.jersey.core.impl.provider.entity.SourceProvider$DOMSourceReader
······
[2019-05-28 15:53:48] [ERROR] - com.netflix.discovery.DiscoveryClient -DiscoveryClient_TEST-DEMO/MSGAPPDEV01V:test-demo:9090 - was unable to send heartbeat!
com.netflix.discovery.shared.transport.TransportException: Cannot execute request on any known server
	at com.netflix.discovery.shared.transport.decorator.RetryableEurekaHttpClient.execute(RetryableEurekaHttpClient.java:112)
	at com.netflix.discovery.shared.transport.decorator.EurekaHttpClientDecorator.sendHeartBeat(EurekaHttpClientDecorator.java:89)
······
```

同时，Eureka Server会出现如下日志信息：

```bash
2019-05-29 11:52:00.780  INFO 63046 --- [nio-8761-exec-1] o.apache.coyote.http11.Http11Processor   : Error parsing HTTP request header
 Note: further occurrences of HTTP request parsing errors will be logged at DEBUG level.

java.lang.IllegalArgumentException: The HTTP header line [""-sw6: 1-NDIuMS4xNTU5MTAxOTIwNTIxMDAwMQ==-NDIuMS4xNTU5MTAxOTIwNTIwMDAwMA==-0-42-42-I2xvY2FsaG9zdDo4NzYx-Iy9ldXJla2EvYXBwcy8=-Iy9ldXJla2EvYXBwcy8=] does not conform to RFC 7230 and has been ignored.
        at org.apache.coyote.http11.Http11InputBuffer.skipLine(Http11InputBuffer.java:953) ~[tomcat-embed-core-9.0.12.jar:9.0.12]
```

## 解决办法

- 请检查是否为探针传了`SW_AGENT_NAMESPACE`变量并将其值设置成了`租户Code`.

# 分布式链路追踪接入
分布式追踪采用探针的方式从Java应用程序中采集数据发送到后端收集器。
因此，我们在加上探针过后一般是需要配置后端收集器地址。

在此之前，你需要从DaoCloud的Nexus仓库或者你自己公司私服处下载探针压缩包，DaoCloud下载地址：`http://nexus.mschina.io/nexus/content/repositories/labs/org/apache/skywalking/dmp/agent/2.0.0/agent-2.0.0.gz`。后面的讲解部分也会用到该地址。

本指南旨在帮助用户快速了解如何安装Java探针。介绍了为[服务Jar包](jar.md)、[War包](war.md)添加探针的方式。同时介绍了[基于容器接入](docker.md)的最佳实践。

在接入过程中遇到问题？试试从[这里](faq/README.md)找到解决办法吧～
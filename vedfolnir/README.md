# 简介

### Vedfolnir 是什么

> 在北欧的神话传说中，Vedfolnir（维德佛尔尼尔）是占据世界之树顶端，一只被赋予观察世界权力的老鹰。
>
> ​																																							——维基百科

Vedfolnir 是一个对实例进行实时数据采集的工具，可以在运行时观测实例 jvm，gc，线程，日志等重要信息，方便实时排查问题。通讯链路使用 websocket ，由原 collector-server 与 collector-manager 二合一。



#### 版本要求

###### vedfolnir-manager

- Jdk 8+

###### vedfolnir-agent

- Jdk 7+
- Spring 3+



#### 支持的功能

- 实时采集监控数据
- 支持应用对接 prometheus



#### 支持的监控内容

- cpu使用，gc，内存等运行时数据
- 实例的基本信息，以及 jvm 参数
- metrics 指标
- 线程
- 环境
- Beans
- 日志管理，包含修改日志级别 (需引入 spring-boot-actuator)

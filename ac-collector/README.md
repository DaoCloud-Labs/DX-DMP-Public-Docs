# 简介

### collector 是什么

collector 是基于 spring-boot-actuator 与 micrometer，对实例进行实时数据采集的工具，可以在运行时观测实例 jvm，gc，线程，日志等重要信息，方便实时排查问题。通讯链路使用 websocket 与 redis，同时兼容 spring boot 1.5.x 以上与 2.x。

#### 支持的监控内容

- cpu使用，gc，内存等运行时数据
- 实例的基本信息，以及 jvm 参数
- metrics 指标，基于 micrometer
- 线程
- 环境
- Beans
- 日志管理，包含修改日志级别

### 为什么需要 collector

通过实时地监控实例详情，可以提早排查可能出现的问题，掌握主动权。



# 架构概览

[![AAtGNR.png](https://s2.ax1x.com/2019/03/14/AAtGNR.png)](https://imgchr.com/i/AAtGNR)



## 组件

- collector-managaer：负责与前端交互，并将指令通过 redis 广播至 collector-server
- collector-server：过滤指令，转发至实例，并处理采集到的数据；定时向实例下发指令收集 metrics 指标，存入 es
- collector-client-starter：在 app 内，基于 micrometer 及 spring-boot-actuator进行数据采集
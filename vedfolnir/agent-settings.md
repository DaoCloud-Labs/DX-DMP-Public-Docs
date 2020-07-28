## 配置参数说明
|  环境变量名   | 默认值  |说明  | 是否必须  |
|  ----  | ----  |---- |----  |
| VEDFOLNIR_WS_DELAY  | 10 s | 探针启动后，与 vedfolnir-manager 连接初始化延迟  |否  |
| VEDFOLNIR_PROMETHEUS_PORT | 8888 | metrics 端口号  | 否  |
| VEDFOLNIR_IS_EXPOSE_PROMETHEUS  | false | 是否需要暴露 metrics 接口  | 否  |
| DX_DMP_ACTUATOR_SERVER  | true | vedfolnir-manager url | 否  |
| VEDFOLNIR_LOG_LEVEL  | DEBUG |  探针日志级别| 否  |
| VEDFOLNIR_LOG_PATH  | {vedfolnir-agent-path}/logs/vedfolnir-agent.log | 探针日志文件路径  | 否  |
| VEDFOLNIR_LOG_BACKUPS  | 50 | 探针日志文件保留数量  | 否  |
| VEDFOLNIR_LOG_MAX_SIZE  | 50 * 1024 (KB) | 探针日志文件大小  | 否  |

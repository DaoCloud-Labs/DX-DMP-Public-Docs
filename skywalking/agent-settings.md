# 探针参数配置
以下内容详细解释了`agent/config/agent.config`配置文件中的参数：

| 配置Key | 环境变量 | 描述 | 默认值 | 例如 |
| ----------- | ---------- | --------- | --------- |
| `agent.env_code` | `DX_ENV_ID ` |  **DX-Pilot环境Code**. | 从DCS部署文件中取`DX_ENV_ID`环境变量值。|`vay9f4-fhoayf9-f94gf8a-vakcbk3-fad33r3` |  
| `agent.properties[dx-application-name]`| `DX_APP_NAME` | **DX-Pilot创建的应用名** | `Your_ApplicationName` | daoshop | 
| `agent.properties[dx-application-id]` | `DX_APP_ID` | **DX-Pilot生成的应用ID** | `Your_ApplicationId` | `fafafa-fe3vaf-8vy8gi-f9ayf4`
| `agent.service_name` | `DX_SERVICE_NAME ` |在 DX-DMP 链路追踪 UI 中展示的服务名。 | `Your_ApplicationName` | daoshop-user-center | 
|`agent.instance_uuid` | `AGENT_INSTANCE_UUID` | 实例ID | `""` | daoshop-user-center-5c9644d98-765gj | 
| `collector.backend_service`| `SW_AGENT_COLLECTOR_BACKEND_SERVICES` |后端Collector收集器的地址，通过逗号分割集群地址。| 127.0.0.1:10800 |`dx-skywalking-oap-ng.dx-pilot.svc:11800`|
|`agent.sample_n_per_3_secs`| `SW_AGENT_SAMPLE` |**默认全部**。每3秒采样的数量。设置为负数代表全样采取。|Not set|`agent.sample_n_per_3_secs=-1`|
|`agent.authentication`| `SW_AGENT_AUTHENTICATION` | **默认未设置**。认证token，与后端application.yml中的设置保持一致。|Not set| `agent.authentication = dangrous` |
|`agent.span_limit_per_segment`| `SW_AGENT_SPAN_LIMIT` |**默认未设置**。在单个segment中最大的span数量，通过此设置，可以节省应用内存成本。|Not set |`agent.span_limit_per_segment=300`|
|`agent.ignore_suffix`| `SW_AGENT_IGNORE_SUFFIX` |**默认未设置**。忽略追踪过程中包含了此前缀的segment。|Not set|`agent.ignore_suffix=.jpg,.jpeg,.js,.css,.png,.bmp,.gif,.ico,.mp3,.mp4,.html,.svg`|
|`agent.is_open_debugging_class`| `SW_AGENT_OPEN_DEBUG` |`默认为false`。如果设置为`true `的话，探针会在 `/debugging` 目录中生成所有被增强的class文件。|Not set|`agent.is_open_debugging_class = true`|
| `logging.level`| `SW_LOGGING_LEVEL` |`默认为DEBUG`。探针日志级别设置。|`DEBUG`|`INFO`|
| `logging.file_name`| `SW_LOGGING_FILE_NAME` |日志文件名称。|`skywalking-api.log`|`logging.file_name=skywalking-collector.log`|
| `logging.dir`| `SW_LOGGING_DIR` |日志文件目录名称。默认使用`system.out`输出日志。|`""`| `myapp/` |
| `logging.max_file_size`| `SW_LOGGING_MAX_FILE_SIZE` |日志文件大小最大值。如果日志超出此阈值，将会切分到新的日志文件中。|`300 * 1024 * 1024`| `200 * 1024 * 1024` |
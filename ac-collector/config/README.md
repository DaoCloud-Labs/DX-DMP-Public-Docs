# 配置解释



## collector-manager

```yaml
# 与 collector-server 保持一致，建立 redis 通讯链路
REDIS_HOST='all-in-one-dev.dmptesting.xyz' \

# dmp eureka
EUREKA_URL='http://all-in-one-dev.dmptesting.xyz:8761/eureka' \

# 与 collector-server 保持一致，用于监听频道
CHANNEL_MANAGER=channelTopicManager \
CHANNEL_SERVER=channelTopicServer \

# http请求的超时时间
HTTP_TIMEOUT='10000' \

```



## collector-server

```yml
# 租户 eureka ，与所监控实例一致
EUREKA_URL='http://all-in-one-dev.dmptesting.xyz:8761/eureka' \

# websocket所暴露的端口号
WEBSOCKET_PORT='8889' \

# 存储用 es 信息
ES_CLUSTER_NODE='all-in-one-dev.dmptesting.xyz:9300' \
ES_CLUSTER_NAME='docker-cluster' \

# 与 collector-manager 保持一致
REDIS_HOST='all-in-one-dev.dmptesting.xyz' \

# 缓冲队列大小
QUEUE_SIZE=1000 \

# 网关地址 和 租户code，根据 code 从网关获取租户 id
PROJECT_ADDRESS='http://all-in-one-dev.dmptesting.xyz:9000' \
PROJECT_CODE=test \

# 定时采集时间 秒
SCHEDULED_FIXEDRATE=5 \

# redis 通信通道，与 collector-manager 保持一致
CHANNEL_MANAGER=channelTopicManager \
CHANNEL_SERVER=channelTopicServer \

```




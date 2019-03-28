# 服务注册与发现介绍

本章主要介绍的是如何实现服务的注册与发现。

## 版本

- 平台内置 Spring Cloud Netflix Eureka Server: `2.0.1.RELEASE`
- 客户端 Spring Cloud Starter Netflix Eureka Client: `>= 1.4.1.RELEASE`

## 介绍

通常来说服务注册与发现包括两部分，一个是服务器端，另一个是客户端。Server 是一个公共的服务，为 Client 提供服务注册和发现的功能，维护注册到自身的 Client 的相关信息，同时提供接口给 Client 获取注册表中其他服务的信息，使得动态变化的 Client 能够进行服务间的调用。Client 将自己的服务信息通过一定的方式登记到 Server 上，并在正常范围内维护自己信息一致性，方便其他服务发现自己，同时可以通过 Server 获取到自己依赖的其他服务信息，完成服务调用。

Spring Cloud Netflix Eureka 是 Spring Cloud 提供用于服务发现和注册的基础组件，是搭建 Spring Cloud 微服务架构的前提之一。Eureka 作为一个开箱即用的基础组件，屏蔽了底层 Server 和 Client 交互的细节，使得开发者能够将精力更多地放在业务逻辑上，加快为服务架构的实施和项目的开发。

在 Netfilx 中，Eureka 是一个 RESTful 风格的服务注册与发现的基础服务组件。Eureka 由两部分组成，一个是 Eureka Server，提供服务注册和发现功能，即我们上面说的服务端；另一个是 Eureka Client，它简化了客户端与服务端之间的交互。Eureka Client 会定时将自己的信息注册到 Eureka Server 中，并从 Server 中发现其他服务。Eureka Client 中内置一个负载均衡器，用来进行基本的负载均衡。

![](https://ws1.sinaimg.cn/large/eddc95fcly1g0t1lm3l2cj20jg0bc0tr.jpg)

## 附录

1. [Eureka 官方 WIKI](https://github.com/Netflix/eureka/wiki)

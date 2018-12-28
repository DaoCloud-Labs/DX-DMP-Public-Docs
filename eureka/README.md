# 服务注册与发现介绍

本章主要介绍的是如何实现服务的注册与发现。

[引入依赖](pom.md)

更多使用可以参考[Eureka官方WIKI](https://github.com/Netflix/eureka/wiki)

## 介绍

通常来说服务注册于发现包括两部分，一个是服务器端，另一个是客户端。Server是一个公共的服务，为Client提供服务住的和发现的功能，维护注册到自身的Client的相关信息，同时提供接口给Client获取注册表中其他服务的信息，使得动态变化的Client能够进行服务间的调用。Client将自己的服务信息通过一定的方式登记到Server上，并在正常范围内维护自己信息一致性，方便其他服务发现自己，同时可以通过Server获取到自己依赖的其他服务信息，完成服务调用。

Spring Cloud Netflix Eureka 是 Spring Cloud 提供用于服务发现和注册的基础组件，是搭建Spring Cloud微服务架构的前提之一。Eureka作为一个开箱即用的基础组件，屏蔽了底层Server和Client交互的细节，使得开发者能够将精力更多地放在业务逻辑上，加快为服务架构的实施和项目的开发。

在Netfilx中，Eureka是一个RESTful风格的服务注册与发现的基础服务组件。Eureka由两部分组成，一个是Eureka Server，提供服务注册和发现功能，即我们上面说的服务端；另一个是Eureka Client，它简化了客户端与服务端之间的交互。Eureka Client会定时将自己的信息注册到Eureka Server中，并从Server中发现其他服务。Eureka Client中内置一个负载均衡器，用来进行基本的负载均衡。

DMP是中使用的注册中心是基于Spring Cloud的Finchley版本。Eureka版本基于1.9.3。
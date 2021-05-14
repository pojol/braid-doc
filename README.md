---
description: braid-go 是一个由golang实现的轻量易读的微服务框架，使用模块化的结构编写，以及提供统一的消息模型。
---

# 简介

> braid 的实现设想，是提供一套`轻量`且`完备`的微服务框架。用户只需要将所需的`模块` 添加到框架中让它运行即可获取他们提供的`服务`。

> 在 braid 中将主要提供两种语义
>
> * **组件**
>    * 将不同的组件`注册`到braid中运行，同时通过组件的 `option` 来控制这个组件的运行逻辑。
> * **消息**
>    *  `RPC` 消息，用于服务和服务之间的消息传递。
>    *  `Pub-sub` 消息，用于组件和组件之间的消息订阅&发布，用户也可以订阅 braid modules 中发布的消息，也可以在消息系统中自定义自己的 `Topic`


### Guide
1. [环境搭建（安装支撑服务](../guide/环境搭建.md)
2. [Hello,braid!](../guide/hello_braid.md)
3. [链式调用](../guide/链式调用.md)
4. [在 Docker swarm 中运行](../guide/docker-swarm.md)


### 模块
* **Mailbox** 消息发布订阅 
  * [mailbox-nsq](modules/mailbox-nsq.md)
* **RPC**
  * [grpc-client](modules/grpc-client.md)
  * [grpc-server](modules/grpc-server.md)
* **Discover** 服务发现
  * [discover-consul](modules/discover-consul.md) 
* **Elector** 选举
  * [elector-consul](modules/elector-consul.md) 
  * [elector-k8s](modules/elector-k8s.md) 
* **LinkCache** 链路缓存
  * [linkcache-redis](modules/linkcache-redis.md) 
* Balancer 负载均衡
  * [random](modules/balancer-random.md)
  * [Smooth Weighted Round-Robin](modules/balancer-swrr.md)
* **Tracer** 分布式系统追踪服务
  * [jaegertracing](modules/tracer-jaeger.md) 



### 工具
* Web
  * sankey chart (观测链路负载情形
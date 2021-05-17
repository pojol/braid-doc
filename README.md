---
description: braid-go 是一个由golang实现的轻量易读的微服务框架，使用模块化的结构编写，以及提供统一的消息模型。
---

# 简介

> braid 的实现设想，是提供一套`轻量`且`完备`的微服务框架。用户只需要将所需的`模块` 添加到框架中让它运行即可获取他们提供的`服务`。

> braid 中主要提供两套系统
1. **模块化**
   * 提供基于模块接口实现的各种微服务，通过注册到braid中运行。
2. **统一的消息模型**
   * RPC 消息，用于服务和服务之间的调用
   * Pub-sub 消息，用于模块和模块之间的消息通知



### Guide
1. [环境搭建（安装支撑服务](../guide/环境搭建.md)
2. [Hello,braid!](../guide/hello_braid.md)
3. [链式调用](../guide/链式调用.md)
4. [在 Docker swarm 中运行](../guide/docker-swarm.md)

### 模块
|**Discovery**|**Balancing**|**Elector**|**RPC**|**Pub-sub**|**Tracer**|**LinkCache**|
|-|-|-|-|-|-|-|
|服务发现|负载均衡|选举|RPC|发布-订阅|分布式追踪|链路缓存|
|[discoverconsul](modules/discover-consul.md)|[balancerrandom](modules/balancer-random.md)|[electorconsul](modules/elector-consul.md)|[grpc-client](modules/grpc-server.md)|[mailbox](modules/mailbox-nsq.md)|[jaegertracer](tracer-jaeger.md)|[linkerredis](https://github.com/pojol/braid-go-go/wiki/Guide-4.-%E4%BD%BF%E7%94%A8Link-cahe)
||[balancerswrr](modules/balancer-swrr.md)|[electork8s](modules/elector-k8s.md)|[grpc-server](modules/grpc-server.md)|||


### 工具
* Web
  * sankey chart (观测链路负载情形
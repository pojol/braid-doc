# Table of contents

* [简介](README.md)

## Guide

* [1. 环境搭建](guide/环境搭建.md)
* [2. Hello,braid!](guide/hello_braid.md)
* [3. 链式调用](guide/链式调用.md)
* [4. Docker swarm](guide/docker-swarm.md)



## Modules

|**Discovery**|**Balancing**|**Elector**|**RPC**|**Pub-sub**|**Tracer**|**LinkCache**|
|-|-|-|-|-|-|-|
|服务发现|负载均衡|选举|RPC|发布-订阅|分布式追踪|链路缓存|
|[discoverconsul](modules/discover-consul.md)|[balancerrandom](modules/balancer-random.md)|[electorconsul](modules/elector-consul.md)|[grpc-client](modules/grpc-server.md)|[mailbox](modules/mailbox-nsq.md)|[jaegertracer](tracer-jaeger.md)|[linkerredis](https://github.com/pojol/braid-go-go/wiki/Guide-4.-%E4%BD%BF%E7%94%A8Link-cahe)
||[balancerswrr](modules/balancer-swrr.md)|[electork8s](modules/elector-k8s.md)|[grpc-server](modules/grpc-server.md)|||
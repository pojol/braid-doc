---
description: link-cache 模块可以保证用户`token`在微服务中的调用，始终走的是固定线路。这将帮助用户在服务中编写一些针对性的本地优化
---

> `注` 使用linkerredis，需要依赖redis

* 使用
* 释放
* 对负载均衡的支持


##### 使用

**在 linkerredis 的实现中主要区分为两种模式**

* LinkerRedisModeRedis
 > `redis` 模式主要被用于访问无序的服务， 例如 `client` -> `gate` 的链路
* LinkerRedisModeLocal
 > `local` 模式主要用于访问有序的服务，例如在`gate`开启`link-cache`之后，`gate` -> `base` 的链路是固定的，因此在`base`中就可以采用`local`模式

> 简单来说就是对外的网关节点使用 redis 模式，内部节点采用 local 模式。

**注册一个以local模式运行的link-cache**

```go
b.RegistModule(
    braid.LinkCache(linkerredis.Name,
        linkerredis.WithRedisAddr(redisAddr),
        linkerredis.WithMode(linkerredis.LinkerRedisModeLocal),  // 默认是redis 
    ),
)
```

##### 释放

**多种不同的释放环境**

* 用户主动释放token （例如用户的数据由热变冷
 > 这个信号由用户主动发出，braid只处理信号
 ```go
braid.Mailbox().Pub(mailbox.Cluster, linkcache.TopicUnlink, &mailbox.Message{
			Body: []byte("Token"),
		})
// 将信号发布出去，由braid进行捕捉就可以了。
 ```
* `service`宕机或者其他原因不可用
* 清理没有被捕获到的`service`离线信息（例如机房断电等

> 宕机或者其他离线行为由braid-go捕获后主动发起，用户不需要编写额外的代码，但需要将module/discover注册到braid中。


##### 对负载均衡的支持
> `暂未实装` 因为在`swarm`模式下 不同`node`的`物理权重`不方便初始化，所以功能暂缓开放

> redislinker 会周期性的将本节点名下的自节点连接信息同步到 `discover` 模块，用于修改模块中 `service` 的物理权重值

```shell
# 节点的权重信息
动态权重 = 物理权重 - 服务当前链接数 （连接数越多，权值越低
```

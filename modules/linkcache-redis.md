---
description: 使用 redis 来实现链路缓存
---

> `注` 使用linkerredis，需要依赖redis

* 选项
* 注册
* Topic

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithRedisAddr | redis 地址 |
| WithRedisMaxIdle | redis client 的最大空闲连接（默认16 |
| WithRedisMaxActive | redis client 的最大活跃连接（默认128 |
| WithSyncTick | 同步服务的权重间隔（默认10s |
| WithMode | linkcache 的执行模式 `local`, `redis` ，默认为 `local` |

| Topic | 作用域 | 概述 |
| ---- | ---- | ---- |
| ServiceLinkNum | Cluster | 服务中的链路信息 |
|TokenUnlink| Cluster  |token 离线信息|

### 注册

```go
b.RegistModule(
    braid.LinkCache(linkerredis.Name,
        linkerredis.WithRedisAddr(redisAddr),
        linkerredis.WithMode(linkerredis.LinkerRedisModeLocal),  // 默认是 local 
    ),
)
```

### Topic

```go
// ⚠️这里因为是集群消息，因此建立channel的时候需要传入唯一id，不然的话有可能会和其他的服务产生竞争。
// 监听服务中的连接数量信息
consumer := mailbox.GetTopic(linkcache.ServiceLinkNum).Sub(Name + "-" + ip)
consumer.Arrived(func(msg *mailbox.Message){
    linkMsg := linkcache.DecodeLinkNumMsg(msg)
    // todo
})

// 这是一条从用户端发出的信息，由集群内的linkcache捕捉并处理。
mailbox.GetTopic(linkcache.TokenUnlink).Pub(&mailbox.Message{ Body : []byte("token") })

```
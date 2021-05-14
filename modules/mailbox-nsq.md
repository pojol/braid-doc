---
description: 基于 nsq 封装的消息发布-订阅
---

### 消息模型

#### API 消息
> API消息主要用于`服务->服务`之间

* ctx 用于分布式追踪，存储调用链路的上下文信息
* target 目标服务节点 例 "mail", braid 会依据服务发现和负载均衡信息，自动将消息发送到合适的节点
* methon 目标节点支持的方法 例 "api.mail/send"
* token 调用者的唯一凭据（用于链路缓存
* args 输入参数
* reply 回复参数
* opts... gpc调用的额外参数选项

```go
client.Invoke(ctx, target, methon, token, args, reply, opts...)
```

#### Pub-sub 消息
> Pub-sub消息主要用于`模块->模块`之间

* 作用域
    * mailbox.ScopeProc 消息作用于`自身进程`中的模块
    * mailbox.ScopeCluster 消息将作用于`整个集群`中的模块
* Topic
    > 当使用 topic.Pub 的时候，消息会往 topic 中的 channel 投递，`每一个channel都会产生一份消息`。
    * 单一接收 （一个topic + channel x 1 : consumer x 1
    * 广播逻辑 （一个topic + channel x N : consumer x N
    * 竞争接收 （一个topic + channel x 1 : consumer x N
* Channel
    > topic 中的管道，每一个管道都会接收到来自topic 的消息，并且每个管道都可以拥有多个`消费者`

####  示例
* `多个`消费者`单个` Channel

```go

topic := "test.procNotify"

mailbox.RegistTopic(topic, mailbox.ScopeProc)

mailbox.GetTopic(topic).Sub("consumer_1").Arrived(func(msg *mailbox.Message) {
    fmt.Println("consumer a receive", string(msg.Body))
})
mailbox.GetTopic(topic).Sub("consumer_1").Arrived(func(msg *mailbox.Message) {
    fmt.Println("consumer b receive", string(msg.Body))
})

for i := 0; i < 5; i++ {
    mailbox.GetTopic(topic).Pub(&mailbox.Message{Body: []byte(strconv.Itoa(i))})
}

# 两个consumer从一个管道中竞争获取消息
consumer a receive 0
consumer b receive 1
consumer a receive 2
consumer b receive 3
consumer a receive 4
```

* `多个`消费者`多个` Channel

```go
topic := "test.clusterBroadcast"

mailbox.RegistTopic(topic, mailbox.ScopeCluster)

mailbox.GetTopic(topic).Sub("consumer_1").Arrived(func(msg *mailbox.Message) {
    fmt.Println("consumer a receive", string(msg.Body))
})
mailbox.GetTopic(topic).Sub("consumer_2").Arrived(func(msg *mailbox.Message) {
    fmt.Println("consumer b receive", string(msg.Body))
})

for i := 0; i < 5; i++ {
    mailbox.GetTopic(topic).Pub(&mailbox.Message{Body: []byte(strconv.Itoa(i))})
}

# 两个consumer从各自的管道中获取
consumer b receive 0
consumer b receive 1
consumer b receive 2
consumer b receive 3
consumer b receive 4
consumer a receive 0
consumer a receive 1
consumer a receive 2
consumer a receive 3
consumer a receive 4
```

#### Benchmark
* Pub-sub ScopeProc

```shell
goos: darwin
goarch: amd64
pkg: github.com/pojol/braid-go/modules/mailboxnsq
BenchmarkTestProc-8   	
 7149286	       171 ns/op	     118 B/op	       3 allocs/op
PASS
```
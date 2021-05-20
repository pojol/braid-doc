---
description: 基于 nsq 封装的消息发布-订阅 ( 额外实现了基于 nsq 消息模型的进程内消息发布-消费功能
---

* 消息模型
* 示例
* Benchmark


#### Pub-sub 消息
> Pub-sub消息主要用于`模块`->`模块`之间

<img src="../images/nsq.gif" width="420" height=281 />

* 作用域
    * mailbox.ScopeProc 消息作用于`自身进程`中的模块
    * mailbox.ScopeCluster 消息将作用于`整个集群`中的模块
* Topic
    > 当使用 topic.Pub 的时候，消息会往 topic 中的 channel 投递，`每一个channel都会产生一份消息`
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

#### **Pub-sub** Benchmark
*  ScopeProc

```shell
$ go test -benchmem -run=^$ -bench ^BenchmarkTestProc -cpu 2,4,8
处理器: 2.2 GHz 四核Intel Core i7
goos: darwin
goarch: amd64
pkg: github.com/pojol/braid-go/modules/mailboxnsq
BenchmarkTestProc-2   4340389   302 ns/op   109 B/op   3 allocs/op
BenchmarkTestProc-4   8527536   151 ns/op   122 B/op   3 allocs/op
BenchmarkTestProc-8   7564869   161 ns/op   118 B/op   3 allocs/op
PASS
```

* ScopeCluster

```shell
$ go test -benchmem -run=^$ -bench ^BenchmarkClusterBroadcast -cpu 2,4,8
腾讯云 4核 8GB
goos: linux
goarch: amd64
BenchmarkClusterBroadcast-2   70556   17234 ns/op   540 B/op   16 allocs/op
BenchmarkClusterBroadcast-4   71202   18975 ns/op   676 B/op   20 allocs/op
BenchmarkClusterBroadcast-8   62098   19037 ns/op   662 B/op   20 allocs/op
```
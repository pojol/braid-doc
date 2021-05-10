---
description: ConsulDiscover 模块，这个模块的主要功能是从注册中心中发现服务节点的变更情况，并发布消息通知到其他模块。
---

* 选项
* 注册到braid
* Topic
* Consumer 样例

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithTag | 发现标签，用于识别需要纳入到发现列表的服务节点 |
| WithBlacklist | 黑名单列表，即便tag是匹配的，但是我依然想排除这个节点 |
| WithSyncServiceInterval | 服务发现的频率（默认2秒 |
| WithConsulAddr | 更改默认的consul地址 |


### 注册到braid

> 将 consul discover 注册到 braid
```go
    b, _ := braid.New(
		"sample",
		mailboxnsq.WithLookupAddr([]string{nsqLookupAddr}),
		mailboxnsq.WithNsqdAddr([]string{nsqdAddr}),
	)

	b.RegistModule(
		braid.Discover(
			discoverconsul.Name,
			discoverconsul.WithConsulAddr(consulAddr),
			discoverconsul.WithTag("sample-service-01"),
		),
	)

	b.Init()
	b.Run()
	defer b.Close()
```

### Topic
```go
discover.Node {
	ID string	// 节点ID
	Name    string  // 节点名 （例 gate | mail ...
	Address string	// 节点地址（例 0.0.0.41
	Weight int // 节点的权重值
}
```

* topic.addService
 > 有一个新的服务节点加入到发现列表中
* topic.removeService
 > 有一个当前的服务节点离开发现列表
* topic.updateService
 > 有一个当前的服务节点有更新

### Consumer 样例
```go
// 获取到 discover 注册的 topic，并在 topic 上创建一个订阅者 
addServiceChannel := mailbox.GetTopic(discover.AddService).Sub()

go func() {
	for {
		select {
			case msg := <-addServiceChannel.OnArrived():
				var nod discover.Node
				nod := discover.DecodeAddServiceMsg(msg)
				// todo
		}
	}
}()
```
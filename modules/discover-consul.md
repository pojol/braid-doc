---
description: 使用 consul 实现服务发现
---

* 选项
* 注册
* Topic

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithTag | 发现标签，用于识别需要纳入到发现列表的服务节点 |
| WithBlacklist | 黑名单列表，即便tag是匹配的，但是我依然想排除这个节点 |
| WithSyncServiceInterval | 服务发现的频率（默认2秒 |
| WithConsulAddr | 更改默认的consul地址 |

| Topic | | |
| ---- | ---- | ---- |
| ServiceUpdate | 服务更新 topic 包含如下3种事件 ||
|| EventAddService | 有新的服务加入 |
||EventRemoveService| 有旧的服务退出 |
||EventUpdateService| 有旧的服务更新 |

### 注册
> 将 consul discover 注册到 braid

```go
    b, _ := braid.New(
		"sample",
		mailboxnsq.WithLookupAddr([]string{nsqLookupAddr}),
		mailboxnsq.WithNsqdAddr([]string{nsqdTCPAddr}, []string{nsqdHTTPAddr}),
	)

	b.RegistModule(
		braid.Discover(
			discoverconsul.Name,
			discoverconsul.WithConsulAddr(consulAddr),
			discoverconsul.WithTag("sample-service-01"),
		),
	)
```


### Topic

```go
discover.Node {
	ID string		// 节点ID
	Name    string  // 节点名 （例 gate | mail ...
	Address string	// 节点地址（例 0.0.0.41
	Weight int 		// 节点的权重值
}
```

```go
// 获取到 discover 注册的 topic，并在 topic 上创建一个订阅者 
topic := mailbox.GetTopic(discover.UpdateService)

c := topic.Sub("Balancer")	// balancer consumer
c.Arrived(func(msg *mailbox.Message){
	var nod discover.Node
	nod := discover.DecodeAddServiceMsg(msg)
	// todo
})

```

> 注：关于 pub-sub 更多的信息请至 [**Mailbox**](mailbox.md) 中查看
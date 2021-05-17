---
description: 使用 consul 实现选举
---

* 选项
* 注册
* Topic

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithConsulAddr | consul地址 |
| WithLockTick | 更新主从信息的频率（默认2秒 |
| WithSessionTick | 更新consul session的频率（默认5秒 |

| Topic | | |
| ---- | ---- | ---- |
| ChangeState | 服务主从信息变更 ||


### 注册
> 将 consul elector 注册到 braid

```go
    b, _ := braid.New(
		"sample",
		mailboxnsq.WithLookupAddr([]string{nsqLookupAddr}),
		mailboxnsq.WithNsqdAddr([]string{nsqdTCPAddr}, []string{nsqdHTTPAddr}),
	)

	b.RegistModule(
		braid.Elector(
			electorconsul.Name,
			electorconsul.WithConsulAddr(consulAddr),
		),
	)
```

### Topic

```go
topic := mailbox.GetTopic(elector.ChangeState)

c := topic.Sub("Linkcache")	// Linkcache consumer
c.Arrived(func(msg *mailbox.Message){
    stateMsg := elector.DecodeStateChangeMsg(msg)
	// todo
})
```
---
description: 使用 consul 实现选举
---

* 接口
* 选项
* Topic
* 注册样例
* Topic样例

### 接口
> 无

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithConsulAddr | consul地址 |
| WithLockTick | 更新主从信息的频率（默认2秒 |
| WithSessionTick | 更新consul session的频率（默认5秒 |

### Topic
| Topic | 作用域 | 概述 |
| ---- | ---- | ---- |
| ChangeState | Proc | 服务主从信息变更 |


### 注册样例

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

### Topic样例

```go
topic := mailbox.GetTopic(elector.ChangeState)

c := topic.Sub("Linkcache")	// Linkcache consumer
c.Arrived(func(msg *mailbox.Message){
    stateMsg := elector.DecodeStateChangeMsg(msg)
	// todo
})
```
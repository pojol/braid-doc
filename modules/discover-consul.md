---
description: 使用 consul 实现服务发现
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
| WithTag | 发现标签，用于识别需要纳入到发现列表的服务节点 |
| WithBlacklist | 黑名单列表，即便tag是匹配的，但是我依然想排除这个节点 |
| WithSyncServiceInterval | 服务发现的频率（默认2秒 |
| WithConsulAddr | 更改默认的consul地址 |


### Topic
| Topic | 作用域 | 概述 |  |
| ---- | ---- | ---- |---- |
| ServiceUpdate | Proc | 服务更新 topic 包含如下3种事件 |
||| EventAddService | 有新的服务加入 |
|||EventRemoveService| 有旧的服务退出 |
|||EventUpdateService| 有旧的服务更新 |

### 注册样例

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


### Topic样例

```go
discover.Node {
	ID string		// 节点ID
	Name    string  // 节点名 （例 gate | mail ...
	Address string	// 节点地址（例 0.0.0.41
	Weight int 		// 节点的权重值
}

// 获取到 discover 注册的 topic
topic := mailbox.GetTopic(discover.UpdateService)
// 在 topic 上创建一个 channel ，并获取到它的 consumer
consumer := topic.Sub("sample_channel")
// 绑定消息到达函数
consumer.Arrived(func(msg *mailbox.Message){
	var nod discover.Node
	nod := discover.DecodeAddServiceMsg(msg)
	// todo
})

```
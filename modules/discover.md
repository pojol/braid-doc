---
description: 服务发现
---

# Discover
> 服务发现模块，这个模块的主要功能是从注册中心中发现服务节点的变更情况，并发布消息通知到其他模块。

### Topic
| 模块提供的信号 ||
|----|----|
| DiscoverAddService | 有一个新的服务节点加入到发现列表中 |
| DiscoverRmvService | 有一个当前的服务节点离开发现列表 |
| DiscoverUpdateService | 有一个当前的服务节点有更新 |

---

### consul-discover 组件
| 模块提供的选项 |  |
| ---- | ---- | 
| WithTag | 发现标签，用于识别需要纳入到发现列表的服务节点 |
| WithBlacklist | 黑名单列表，即便tag是匹配的，但是我依然想排除这个节点 |
| WithSyncServiceInterval | 服务发现的频率（默认2秒 |
| WithConsulAddr | 更改默认的consul地址 |
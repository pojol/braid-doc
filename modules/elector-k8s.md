---
description: 使用 kubernetes 实现选举
---

* 选项
* 注册
* Topic

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithKubeConfig | 传入k8s配置（默认 k8sClient.InClusterConfig |
| WithNodID |  |
| WithNamespace | 传入k8s namespace（默认 default |
| WithRetryTick | 非master节点的重试时间（默认2秒 |

| Topic | | |
| ---- | ---- | ---- |
| ChangeState | 服务主从信息变更 ||


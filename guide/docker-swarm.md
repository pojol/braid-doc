---
description: 将 braid 部署到docker swarm 中
---

1. 升级linux内核 3.18+ （需要内核对overlay2的支持
2. 这里使用的 Engine.version = `20.10.2` Api.version = `1.41`
3. 初始化 docker swarm
4. 创建 overlay 网络
5. 将特定端口打开
6. 使用 docker service 启动 braid-go 节点
---
description: 默认的负载均衡模块 balancer/balancerswrr 平滑加权负载均衡
---

# 5. 负载均衡

> 在 module/client 调用invoke 执行实际上的rpc请求前，会先从发现的服务中中选取一个服务，这个选取的过程主要是由module/balancer提供
>
> 负载均衡是一个内部的支撑服务，用户可以编写自己的负载均衡算法，也可以使用braid默认提供的负载均衡算法，不需要进行注册。

* 选取
* 其他

## 选取

> balancer/balancerswrr 中 pick 函数采用的平滑加权轮询

```go
# 这里简单解释下平滑加权轮询算法，假设有2个节点 A & B
1. 使用普通轮询
A,B,A,B,A,B
2. 使用加权轮询 A = 1 + 3, B = 1
A,A,A,A,B,
3. 使用平滑加权轮询（会进行平滑插值，以保障访问尽可能不涌入一个高权节点
A,A,B,A,A,A,A,B,A,A
```


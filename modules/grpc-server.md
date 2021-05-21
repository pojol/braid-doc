---
description: grpc-server 的封装
---

* 接口
* 选项
* Topic
* 注册样例
* Topic样例

### 接口
> 略

### 选项
| 提供的选项 |  |
| ---- | ---- | 
| WithListen | 提供 server 侦听地址 |
| WithRecover | 提供 grpc-server 的recover 函数 |

### Topic
> 略

### 注册样例
```go

b.RegistModule(
    braid.Server(
        grpcserver.Name,
        grpcserver.WithListen(":14201"),
        grpcserver.WithRecover(func(r interface{}) error {
            err, ok := r.(error)
            if !ok {
                err = fmt.Errorf("%v", r)
            }
            stack := make([]byte, 4<<10) // 4kb
            length := runtime.Stack(stack, true)
            return fmt.Errorf("[GRPC-SERVER RECOVER] err: %v stack: %s", err, stack[:length])
        }),
    ),
)

gpb.RegisterBaseServer(braid.GetServer().(*grpc.Server), &routes.BaseServer{})

```

### Topic样例
> 略

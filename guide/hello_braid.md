# 2. Hello braid

> 这一页将引导用户，运行一个带有`braid`的服务，尝试注册模块，和使用一些braid含有的功能。
>
> 使用 go 1.13+ 进行演示



* 搭建工程
* 示例代码
* 编写Dockerfile
* 运行&测试
* 延展



##### 搭建环境

> 构建编程环境

```shell
$ mkdir hello_braid
$ cd hello_braid
$ vim main.go `:wq` #创建 main.go 文件
$ go mod init hello_braid
$ go get github.com/pojol/braid@v1.2.11
```


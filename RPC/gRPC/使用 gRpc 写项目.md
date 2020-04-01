# 使用 gRPC 写项目

[TOC]

如何写gRPC的 server 端, client 端,以及 proto 文件就不多说了, 自行在网上查找; 既然 gRPC 是为微服务而生,  但是在真的写项目的时候, 该如何使用 gRPC搭建项目微服务架构呢? server 端和 client 端 该处在微服务中的哪一块架构中呢? proto 文件到底在哪儿书写和保存呢? 以下是自身的一些理解:

## 微服务

微服务是指按一定的场景或者业务分成不同的项目, 即程序员中的工程或者 git项目; 但是这些项目之间存在一定的联系和交互, 这种交互是通过远程请求的方式完成, 例如: restAPI, gPRC, jsoncRPC...

## gRPC微服务

### 分类

* server 端: 提供具体的实现逻辑, 通过 proto 端中方法 注册服务  以供 client 端请求

* client 端: 发送请求者, 通过 proto端中方法 实现调用 server 端的 方法

* proto 端: 生成gRPC 客户端 和 服务器 代码
  * 其中 server 端和 client 端都依赖 proto 端
  * client 端请求 server 端 通过 proto

### 搭建项目

1. 创建 proto 项目, 

2. 创建 server 端项目, 建立与 proto 的依赖, 并完善 proto

3. 创建 client 项目, 建立与 proto 的依赖, 只能依赖, 没有管理权

* 总结:

  * 已创建 3 个项目

  * server 和 proto 为提供方所有

  * client 为调用方所有, 调用方必须准守 proto 中所提供的方法

* 问答:
	* 为什么将 proto 单独建立项目?
	  * 最小最简提供调用者对 proto 的依赖包
	* 为什么 proto 归 server 所有
	  * 因为只有 server 知道可以提供怎样的服务


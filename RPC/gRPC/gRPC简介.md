# gRPC 简介

[TOC]

gRPC 一开始由 google 开发，是一款语言中立、平台中立、开源的远程过程调用(RPC)系统。[gRPC 官方文档中文版](http://doc.oschina.net/grpc?t=58008)

## gRPC 是什么

在 gRPC 里*客户端* 应用可以像调用本地对象一样直接调用另一台不同的机器上*服务端*应用的方法，使得您能够更容易地创建分布式应用和服务。与许多 RPC 系统类似，gRPC 也是基于以下理念：

* 定义一个*服务*，指定其能够被远程调用的方法（包含参数和返回类型）。在服务端实现这个接口，并运行一个 gRPC 服务器来处理客户端调用。在客户端拥有一个*存根*能够像服务端一样的方法。

  ![grpc_concept_diagram_00](gRPC简介.assets/grpc_concept_diagram_00.png)


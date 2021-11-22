# wire

详见: https://medium.com/@dche423/master-wire-cn-d57de86caa1b



### 使用时的注意事项

在生成 wire_gen.go文件时, 通过命令: `wire`

**注意:** 执行`wire`应该在当前 wire.go的目录下, 要不然会报 `undeclared name: newApp` 或者不会重新生成 wire_gen.go文件



## 理解:

Wire does not support multiple bindings of the same type, in this case string.

You'll have to create a type alias for at least one of those strings in order to disambiguate.



## kratos 框架中, 如果使用多 services, 则需要修改

1. internal/server/http.go 和internal/server/grpc.go 中的入参, 注册多个services
2. 对应的 internal/biz, internal/data中增加相应的服务极客, 实践依据是 "驱动领域模型"
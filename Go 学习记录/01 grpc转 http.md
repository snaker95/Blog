# 01 grpc 转http 协议

## 参考

https://lixueduan.com/post/grpc/07-grpc-gateway/



## 代码

```shell
├── cmd
│   └── demo
│       ├── grpc
│       │   └── main.go
│       └── grpc2http
│           └── main.go
├── go.mod
├── go.sum
└── proto
    ├── demo
    │   ├── helloworld.pb.go
    │   ├── helloworld.pb.gw.go
    │   ├── helloworld.proto
    │   └── helloworld_grpc.pb.go
    └── google
        └── api
            ├── annotations.proto
            └── http.proto
```

cmd/demo/grpc/main.go

```go
package main

import (
	"context"
	"flag"
	"fmt"
	"google.golang.org/grpc"
	pb "grpc2httptest/proto/demo"
	"log"
	"net"
)

var port = flag.Int("port", 50051, "the port to serve on")

type server struct {
	pb.UnimplementedHelloServiceServer
}

func (s *server) Hello(ctx context.Context, in *pb.StringMessage) (*pb.StringMessage, error) {
	return &pb.StringMessage{Value: "hello " + in.Value}, nil
}

func (s *server) Say(ctx context.Context, in *pb.StringMessage) (*pb.StringMessage, error) {
	return &pb.StringMessage{Value: "hello " + in.Value}, nil
}

func main() {
	// Create a gRPC server object
	s := grpc.NewServer()
	// Attach the Greeter service to the server
	pb.RegisterHelloServiceServer(s, &server{})
	// Serve gRPC Server
	lis, err := net.Listen("tcp", fmt.Sprintf(":%d", *port))
	if err != nil {
		log.Fatalf("failed to listen: %v", err)
	}
	log.Println("Serving gRPC on 0.0.0.0" + fmt.Sprintf(":%d", *port))
	if err := s.Serve(lis); err != nil {
		log.Fatalf("failed to serve: %v", err)
	}

	log.Println("Serving gRPC on http://0.0.0.0" + fmt.Sprintf(":%d", *port))
}

```

cmd/demo/grpc2http

```go
package main

import (
	"context"
	"flag"
	"fmt"
	"github.com/grpc-ecosystem/grpc-gateway/v2/runtime"
	"google.golang.org/grpc"
	pb "grpc2httptest/proto/demo"
	"log"
	"net/http"
)

var port = flag.Int("port", 50051, "the port to serve on")
var restful = flag.Int("restful", 9080, "the port to restful serve on")

func main() {
	// 1. 启动 HTTP 服务
	// Create a client connection to the gRPC server we just started
	// This is where the gRPC-Gateway proxies the requests
	conn, err := grpc.Dial(
		fmt.Sprintf("localhost:%d", *port),
		grpc.WithInsecure(),
	)
	if err != nil {
		log.Fatalln("Failed to dial server:", err)
	}

	gwmux := runtime.NewServeMux()
	// Register Greeter
	err = pb.RegisterHelloServiceHandler(context.Background(), gwmux, conn)
	if err != nil {
		log.Fatalln("Failed to register gateway:", err)
	}

	gwServer := &http.Server{
		Addr:    fmt.Sprintf(":%d", *restful),
		Handler: gwmux,
	}

	log.Println("Serving gRPC-Gateway on http://0.0.0.0" + fmt.Sprintf(":%d", *restful))
	log.Fatalln(gwServer.ListenAndServe())
}
```

## 一键生成go 代码 和 swagger 文档

```shell
protoc --proto_path=./proto \
   --go_out=./proto --go_opt=paths=source_relative \
  --go-grpc_out=./proto --go-grpc_opt=paths=source_relative \
  --grpc-gateway_out=./proto --grpc-gateway_opt=paths=source_relative \
  --swagger_out=logtostderr=true:. \
  ./proto/*/*.proto
```

## 一键生成 swagger 文档

```shell
protoc --swagger_out=logtostderr=true:. ./demo/*.proto
```



## 重点

1. proto 书写
2. proto 支持 grpc-getway中 option 语法
3. 健壮的main.go 启动程序

## 不足

1. grpc 转 http 协议, 无法支持form-data 和x-www-form-urlencoded格式, 只支持 application/json
2. 转换后, 保持 grpc 格式, 非 http 通用格式, 即 code,message,data; 需要做封装 参考:https://www.zhangjiee.com/topic/grpc/grpc-to-restful-json-api.html  注意: 引入的 protobuf/proto 的包, 是"google.golang.org/protobuf/proto"

3. 将 grpc 服务中, 封装 grpc 的 error, 扩展 code 值, 满足交互需求
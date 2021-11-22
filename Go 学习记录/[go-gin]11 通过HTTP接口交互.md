# 通过 HTTP 协议交互

[TOC]

在微服务盛行的时代里, 微服务间的交互也多种多样, 有 grpc、Restful; 所以, 封装统一的请求体就很有必要了

### 封装理论

* 请求超时时长设置
* 重试机制

### 封装思路

* 封装 request 体
  * 设置context 上下文
  * 设置 debug
  * 设置超期时间
  * 设置代理
  * 设置是否读取缓存
  * 设置是否重定向
  * 设置 cookie
  * 设置 header
  * 设置UserAgent
  * 增加 query 参数 params
  * 设置认证
  * 设置 body
  * 设置rawBody
  * 设置 formBody
  * 设置SetJsonBody
  * 设置SetMultipartBody
  * 设置回调函数
  * 设置请求SetClient
* 封装 response
  * 



### 初始化项目

```shell
mkdir http-client
cd http-client
go mod init http-client
```







```go
//  重试机制
func httRetry(ctx context.Context, method func() error) error {
	var err error
	var (
		i          = 1
		retryCount = 2
		doChan     = make(chan interface{}, 1)
	)
	doChan <- nil
	defer close(doChan)
	// context 中获取重试次数
	retry := ctx.Value(HTTPRetry)
	if retry != nil {
		retryCount = retry.(int)
	}
	for {
		select {
		case <-doChan:
			{
				err = method()
				if err != nil && i < retryCount {
					i++
					switch err.(type) {
					case *url.Error, *HTTPRespErr: // 自定义报错, 和 net 请求错误
						doChan <- nil
						// 随机等待, 减轻下游服务并发压力
						time.Sleep(time.Duration(rand.Intn(10)) * time.Microsecond)
					}
				}
			}
		default:
			// 跳出循环
			return err
		}
	}


	return err
}
```


## 自定义带 code 的 error

[TOC]

通常情况下, error 只是错误提示的字符串, 并实现接口 Error()方法, 在一些应用场景中, error 中带有 code 更容易沟通和实现个性化错误展示, 所以 实现一个这样的 error, 在接口调用返回

```go
package coderror

// 自定义 error, 带有 code

import (
	"fmt"
)

// CodeError 带有错误码的 error
type CodeError struct {
	Code int64  `json:"code"`
	Msg  string `json:"msg"`
}

// Error 实现 error 接口方法
func (err *CodeError) Error() string {
	return fmt.Sprintf("%s [code=%d]", err.Msg, err.Code)
}

// Wrap 解析error 到对应的 code
func Wrap(err error, code int64) error {
	if err != nil {
		return &CodeError{
			Code: code,
			Msg:  err.Error(),
		}
	}
	return nil
}
```




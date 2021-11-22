## api 接口返回 response 标准化

[TOC]

依赖于自定义带有 code 的 error 实现 response 返回格式标准化:

```go
package payloads

// response 返回格式标准化
// 举例:
// payloads.HandleResp(c, err, data)

import (
	"SnakerGin/pkg/coderror"
	"github.com/gin-gonic/gin"
	"net/http"
)

// Resp HTTP返回值
type Resp struct {
	Message string      `json:"message"`
	Code    int64       `json:"code"`
	Data    interface{} `json:"data"`
}

// getRes get result
func getRes(code int64, msg string) *Resp {
	res := &Resp{}
	res.Code = code
	res.Message = msg
	return res
}

// HandleResp 接口返回 resp 标准化
func HandleResp(c *gin.Context, err error, data ...interface{}) {
	var (
		msg  string = "success"
		code int64  = 0
	)

	switch e := err.(type) {
	case *coderror.CodeError: // 自定义解析
		{
			if e != nil {
				code = e.Code
				msg = e.Error()
			}
		}

	default:
		{
			if e != nil {
				code = -1
				msg = e.Error()
			}
		}
	}

	res := getRes(code, msg)
	if len(data) > 0 { // 弱水三千只取一瓢
		res.Data = data[0]
	}
	c.JSON(http.StatusOK, res)
}
```


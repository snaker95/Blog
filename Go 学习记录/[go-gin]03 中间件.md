# 02 中间件

[TOC]

## 基本需求

* 接口追踪, 通过植入 x-request-id 完成同一组日志的追踪定位
* 解析请求参数, 将请求参数统一写入 gin.Context, 方便之后获取
* 日志中间件, 将入参request和输出response记录到日志中, 方便排查, 其中, 输出response 进行压缩编码, 达到脱敏和节约空间
* 验签中间件, 统一将验签信息放在 header 中

## 接口追踪中间件

```go
package middleware

import (
	"github.com/gin-gonic/gin"
	"github.com/rs/xid"
)

const (
	// request_id
	REQUESTID = "x-request-id"
)

// RequestIdMiddleware request_id 中间件
// 设置 x-request-id
func RequestIdMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {
		requestIdFunc := func(c *gin.Context) string {
			if rid := c.GetHeader(REQUESTID); rid != "" {
				return rid
			}

			rid := xid.New().String()
			return rid
		}
		requestID := requestIdFunc(c)
		c.Set(REQUESTID, requestID)
		c.Next()
	}
}
```

## Recover 中间件

主要来源于 go-gin中 Recover()方法, 重新定义 logger, 统一日志输出

```go
package middleware

import (
	"SnakerGin/config"
	"bytes"
	"fmt"
	"github.com/gin-gonic/gin"
	"io/ioutil"
	"net"
	"net/http"
	"net/http/httputil"
	"os"
	"runtime"
	"strings"
	"time"
)

// Copyright 2014 Manu Martinez-Almeida.  All rights reserved.
// Use of this source code is governed by a MIT style
// license that can be found in the LICENSE file.

var (
	dunno     = []byte("???")
	centerDot = []byte("·")
	dot       = []byte(".")
	slash     = []byte("/")
)

const (
	reset = "\033[0m"
)

// Recovery returns a middleware that recovers from any panics and writes a 500 if there was one.
func RecoveryMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {
		// 修改重点, - 替换原有 logger
		var logger = config.GetLogger(c)
		defer func() {
			if err := recover(); err != nil {
				// Check for a broken connection, as it is not really a
				// condition that warrants a panic stack trace.
				var brokenPipe bool
				if ne, ok := err.(*net.OpError); ok {
					if se, ok := ne.Err.(*os.SyscallError); ok {
						if strings.Contains(strings.ToLower(se.Error()), "broken pipe") || strings.Contains(strings.ToLower(se.Error()), "connection reset by peer") {
							brokenPipe = true
						}
					}
				}
				if logger != nil {
					stack := stack(3)
					httpRequest, _ := httputil.DumpRequest(c.Request, false)
					headers := strings.Split(string(httpRequest), "\r\n")
					for idx, header := range headers {
						current := strings.Split(header, ":")
						if current[0] == "Authorization" {
							headers[idx] = current[0] + ": *"
						}
					}
					if brokenPipe {
						logger.Errorf("%s\n%s%s", err, string(httpRequest), reset)
					} else if gin.IsDebugging() {
						logger.Errorf("[Recovery] %s panic recovered:\n%s\n%s\n%s%s",
							timeFormat(time.Now()), strings.Join(headers, "\r\n"), err, stack, reset)
					} else {
						logger.Errorf("[Recovery] %s panic recovered:\n%s\n%s%s",
							timeFormat(time.Now()), err, stack, reset)
					}
				}

				// If the connection is dead, we can't write a status to it.
				if brokenPipe {
					c.Error(err.(error)) // nolint: errcheck
					c.Abort()
				} else {
					c.AbortWithStatus(http.StatusInternalServerError)
				}
			}
		}()
		c.Next()
	}
}

// stack returns a nicely formatted stack frame, skipping skip frames.
func stack(skip int) []byte {
	buf := new(bytes.Buffer) // the returned data
	// As we loop, we open files and read them. These variables record the currently
	// loaded file.
	var lines [][]byte
	var lastFile string
	for i := skip; ; i++ { // Skip the expected number of frames
		pc, file, line, ok := runtime.Caller(i)
		if !ok {
			break
		}
		// Print this much at least.  If we can't find the source, it won't show.
		fmt.Fprintf(buf, "%s:%d (0x%x)\n", file, line, pc)
		if file != lastFile {
			data, err := ioutil.ReadFile(file)
			if err != nil {
				continue
			}
			lines = bytes.Split(data, []byte{'\n'})
			lastFile = file
		}
		fmt.Fprintf(buf, "\t%s: %s\n", function(pc), source(lines, line))
	}
	return buf.Bytes()
}

// source returns a space-trimmed slice of the n'th line.
func source(lines [][]byte, n int) []byte {
	n-- // in stack trace, lines are 1-indexed but our array is 0-indexed
	if n < 0 || n >= len(lines) {
		return dunno
	}
	return bytes.TrimSpace(lines[n])
}

// function returns, if possible, the name of the function containing the PC.
func function(pc uintptr) []byte {
	fn := runtime.FuncForPC(pc)
	if fn == nil {
		return dunno
	}
	name := []byte(fn.Name())
	// The name includes the path name to the package, which is unnecessary
	// since the file name is already included.  Plus, it has center dots.
	// That is, we see
	//	runtime/debug.*T·ptrmethod
	// and want
	//	*T.ptrmethod
	// Also the package path might contains dot (e.g. code.google.com/...),
	// so first eliminate the path prefix
	if lastSlash := bytes.LastIndex(name, slash); lastSlash >= 0 {
		name = name[lastSlash+1:]
	}
	if period := bytes.Index(name, dot); period >= 0 {
		name = name[period+1:]
	}
	name = bytes.Replace(name, centerDot, dot, -1)
	return name
}

func timeFormat(t time.Time) string {
	var timeString = t.Format("2006/01/02 - 15:04:05")
	return timeString
}
```



## 解析请求参数中间件

```go
package middleware

import (
	"encoding/json"
	"fmt"
	"github.com/gin-gonic/gin"
	"strings"
)

// ParamsMiddleware 请求参数中间件
// 将参数 set 到 c.Request.URL.Path 中
func ParamsMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {
		payload := getParams(c)
		c.Set(c.Request.URL.Path, payload)
		c.Next()
	}
}

// getParams 获取 body 参数
func getParams(c *gin.Context) map[string]interface{} {
	var params = make(map[string]interface{})
	ct := c.Request.Header.Get("Content-Type")
	cl := c.Request.ContentLength
	switch {
	case strings.Contains(ct, "multipart/form-data"):
		if err := c.Request.ParseMultipartForm(cl); err != nil {
			fmt.Printf("ParseMultipartForm err %s", err.Error())
		}

	case strings.EqualFold(ct, "application/x-www-form-urlencoded"):
		if err := c.Request.ParseForm(); err != nil {
			fmt.Printf("ParseForm err %s", err.Error())
		}

	case strings.EqualFold(ct, "application/json"):
		buf := make([]byte, 1024)
		c.Request.Body.Read(buf)
		json.Unmarshal(buf[:cl], &params)
	default:
		fmt.Printf("Content-Type is unknow: %s ", ct)

	}
	return formatForm(c, params)
}

// formatForm 格式化 Form
func formatForm(c *gin.Context, params map[string]interface{}) map[string]interface{} {
	for k, v := range c.Request.Form {
		params[k] = v[len(v)-1]
	}
	for k, v := range c.Request.URL.Query() {
		params[k] = v[len(v)-1]
	}
	return params
}
```

## 日志中间件

```go
package middleware

import (
   "SnakerGin/logger"
   "SnakerGin/utils"
   "bytes"
   "github.com/gin-gonic/gin"
   "github.com/sirupsen/logrus"
   "time"
)

type bodyLogWriter struct {
   gin.ResponseWriter
   body *bytes.Buffer
}

func (w bodyLogWriter) Write(b []byte) (int, error) {
   w.body.Write(b)
   return w.ResponseWriter.Write(b)
}
func (w bodyLogWriter) WriteString(s string) (int, error) {
   w.body.WriteString(s)
   return w.ResponseWriter.WriteString(s)
}

// LoggerMiddleware 路由日志中间件
func LoggerMiddleware() gin.HandlerFunc {
   return func(c *gin.Context) {
      bodyLogWriter := &bodyLogWriter{body: bytes.NewBufferString(""), ResponseWriter: c.Writer}
      c.Writer = bodyLogWriter
      // 日志文件
      logger := logger.GetLogger(c)
      // 开始时间
      startTime := time.Now()
      // 处理请求
      c.Next()
      // 结束时间
      endTime := time.Now()
      // 执行时间
      latencyTime := endTime.Sub(startTime)
      // 请求方式
      reqMethod := c.Request.Method
      // 请求路由
      reqUrl := c.Request.RequestURI
      // 状态码
      statusCode := c.Writer.Status()
      // 请求ip
      clientIP := c.ClientIP()
      // params
      params := c.Value(c.Request.URL.Path)
      // response 将结果压缩输出(脱敏+压缩)
      response := utils.CompressAndEncode(bodyLogWriter.body.String())

      // 日志格式
      logger.WithFields(logrus.Fields{
         "status_code":  statusCode,
         "latency_time": latencyTime,
         "client_ip":    clientIP,
         "req_method":   reqMethod,
         "req_uri":      reqUrl,
         "params":       params,
         "response":     response,
      }).Info()
      c.Next()
   }
}
```

```go
package utils

import (
   "bytes"
   "compress/gzip"
   "encoding/base64"
   "io/ioutil"
)

// CompressAndEncode ...
func CompressAndEncode(dat string) string {
   var buf bytes.Buffer
   zw := gzip.NewWriter(&buf)
   _, err := zw.Write([]byte(dat))
   if err != nil {
      return ""
   }
   if err := zw.Close(); err != nil {
      return ""
   }
   return base64.StdEncoding.EncodeToString(buf.Bytes())
}

// DecompressionAndDecode ...
func DecompressionAndDecode(dat string) string {
   data, err := base64.StdEncoding.DecodeString(dat)
   if err != nil {
      return ""
   }
   reader, err := gzip.NewReader(bytes.NewReader(data))
   if err != nil {
      return ""
   }
   defer reader.Close()
   msg, err := ioutil.ReadAll(reader)
   if err != nil {
      return ""
   }
   return string(msg)
}
```

## 验签中间件

```go
package middleware

import (
	"SnakerGin/config"
	"crypto"
	"crypto/hmac"
	"crypto/md5"
	"encoding/base64"
	"encoding/hex"
	"encoding/json"
	"fmt"
	"github.com/gin-gonic/gin"
	"net/url"
	"reflect"
	"sort"
	"strings"
	"time"
)

const (
	HeaderSignAuth   = "Sign-Auth"
	SignAuthFailCode = -401
)

type headerSignAuth struct {
	ApiKey  string `json:"api_key" sign:"api_key"`
	Sign    string `json:"sign"`
	Expires int    `json:"expires" sign:"expires"`
	Nonce   string `json:"nonce" sign:"nonce"`
}

// SignAuthMiddleware 验签中间件
// 将验签信息放入 header 头 - Sign-Auth {api_key, sign, expires, nonce}
func SignAuthMiddleware() gin.HandlerFunc {
	// 由于 params 中间件 将 请求参数放在 c.Request.Url.Path 的 key 中
	// 所以, 将验证这些参数 和 header 中的是否一致
	return func(c *gin.Context) {
		// 跳过验签
		if ok := config.GetApiSkipSecretConfig()[c.Request.URL.Path]; ok {
			return
		}
		// 获取眼前信息
		signAuthStr := c.GetHeader(HeaderSignAuth)
		if len(signAuthStr) == 0 {
			checkSignFail(c, "无验签信息")
			return
		}
		var sighAuth *headerSignAuth
		if err := json.Unmarshal([]byte(signAuthStr), &sighAuth); err != nil {
			checkSignFail(c, "验签信息错误")
			return
		}
		if sighAuth == nil ||
			len(sighAuth.ApiKey) == 0 ||
			len(sighAuth.Nonce) == 0 ||
			len(sighAuth.Sign) == 0 {

			checkSignFail(c, "验签信息为空")
			return
		}
		// 1. 验证 expires 有效性
		if time.Now().Unix() > int64(sighAuth.Expires) {
			checkSignFail(c, "验签过期")
			return
		}
		// 2. 计算参数的sign, 并比较sighAuth.sign
		params, _ := c.Value(c.Request.URL.Path).(map[string]interface{})
		signature, err := getSign(sighAuth, params)
		if err != nil {
			checkSignFail(c, err.Error())
			return
		}

		if signature != sighAuth.Sign {
			checkSignFail(c, "签名错误")
			return
		}

		c.Next()
	}

}

// checkSignFail 验签失败
func checkSignFail(c *gin.Context, msg string) {
	c.JSON(0, map[string]interface{}{"code": SignAuthFailCode, "message": msg})
	c.Abort()
}

func getSecret(appKey string) string {
	return config.GetApiSecretConfig()[appKey]
}

func getSign(authSign *headerSignAuth, params map[string]interface{}) (string, error) {
	secret := getSecret(authSign.ApiKey)
	if len(secret) == 0 {
		return "", fmt.Errorf("api_key不符合")
	}
	// 将验签数据放入, 进行校验
	elem := reflect.ValueOf(authSign).Elem()
	relType := elem.Type()
	for i := 0; i < relType.NumField(); i++ {
		if tag := relType.Field(i).Tag.Get("sign"); len(tag) > 0 {
			params[tag] = elem.Field(i).Interface()
		}
	}

	keys := make([]string, 0, len(params))
	for key := range params {
		keys = append(keys, key)
	}
	sort.Strings(keys)

	strToSign := ""
	for i, key := range keys {
		if i != 0 {
			strToSign += "&"
		}
		val := params[key]
		strToSign += key + "=" + strings.Replace(url.PathEscape(fmt.Sprintf("%v", val)), "~", "%7E", -1)
	}
	mac := hmac.New(crypto.SHA256.New, []byte(secret))

	if _, err := mac.Write([]byte(strToSign)); err != nil {
		return "", fmt.Errorf("计算签名错误")
	}

	hmacResult := mac.Sum(nil)
	encoded := base64.StdEncoding.EncodeToString(hmacResult)
	hash := md5.New()
	if _, err := hash.Write([]byte(encoded)); err != nil {
		return "", fmt.Errorf("计算签名错误")
	}

	signature := hex.EncodeToString(hash.Sum(nil))[5:15]

	return signature, nil
}
```


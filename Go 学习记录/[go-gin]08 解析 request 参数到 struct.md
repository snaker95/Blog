## 解析 request 中参数

[TOC]

在记录[中间件](./[go-gin]03 中间件.md)的时候, 其中有介绍关于 Param 的中间件, 基本做法是, 将参数(post, get..)通过 `map[string]interface{}` set 到 context.Context 中, 接下来, 将这个数据取出来, 解析到对应的 struct 中, 便于传递和交互

因此, 需要选择或者开发 map 与 struct 转换

### 常见方法

* 曲线**救国**
  * 通过标准库 sync/json
* 直接**反抗**
  * 选择或者开发直接 map转 struct



### 曲线**救国**

通过 json库中 Marshal 和 Unmarshal实现

```go
type Payload struct {
	// ...
}
b, err := json.Marshal(params)
if err != nil {
  return
}
payload := &Payload{}
err = json.Unmarshal(b, payload)
if err != nil {
  return
}
```

* 优点
  * 简单省事
* 缺点
  * 据说具有性能问题, 具体待测试, 和对实现的理解

### 直接**反抗**

通过引用第三方 `github.com/mitchellh/mapstructure`,封装后, 实现 (1) tag 识别为 json, (2) 实现弱类型自动转化

```go
package map2struct

import (
	"encoding/json"
	"github.com/mitchellh/mapstructure"
	"reflect"
)

// 测试性能对比
func UnmarshalJson(kv map[string]interface{}, v interface{}) error {
	b, err := json.Marshal(kv)
	if err != nil {
		return err
	}
	return json.Unmarshal(b, v)
}

// Unmarshal 自定义
func Unmarshal(kv map[string]interface{}, v interface{}) error {
	rv := reflect.ValueOf(v)
	if rv.Kind() != reflect.Ptr || rv.IsNil() {
		return &InvalidUnmarshalError{reflect.TypeOf(v)}
	}
	config := &mapstructure.DecoderConfig{
		Metadata:         nil,
		Result:           v,
		WeaklyTypedInput: true,
		TagName:          "json",
	}
	decoder, err := mapstructure.NewDecoder(config)
	if err != nil {
		return nil
	}
	return decoder.Decode(kv)
}



// An InvalidUnmarshalError describes an invalid argument passed to Unmarshal.
// (The argument to Unmarshal must be a non-nil pointer.)
type InvalidUnmarshalError struct {
	Type reflect.Type
}

func (e *InvalidUnmarshalError) Error() string {
	if e.Type == nil {
		return "map2struct: Unmarshal(nil)"
	}

	if e.Type.Kind() != reflect.Ptr {
		return "map2struct: Unmarshal(non-pointer " + e.Type.String() + ")"
	}
	return "map2struct: Unmarshal(nil " + e.Type.String() + ")"
}
```

### 大比拼

在代码中, 完成了 2 中实现, 然后进行了对比

```go
package map2struct

import (
	"fmt"
	"testing"
	"time"
)

type ExampleReq struct {
	Code int64 `json:"code,string"`
}

func TestUnmarshal(t *testing.T) {
	kv := map[string]interface{}{
		"code": "12",
	}
	var ret *ExampleReq
	start := time.Now()
	for range []int{1000:0} {
		Unmarshal(kv, ret)
	}
	fmt.Println(time.Since(start))
}

func TestUnmarshalJson(t *testing.T) {
	kv := map[string]interface{}{
		"code": "12",
	}
	var ret *ExampleReq
	start := time.Now()
	for range []int{1000:0} {
		UnmarshalJson(kv, ret)
	}
	fmt.Println(time.Since(start))
}
```

* 结果

  | 方式\耗时\量级           | 单次      | 百        | 千         | 万          | 十万         | 百万         |
  | ------------------------ | --------- | --------- | ---------- | ----------- | ------------ | ------------ |
  | 曲线救国 (json)          | 224.046µs | 574.793µs | 5.097013ms | 27.617239ms | 411.802196ms | 2.573789103s |
  | 直接反抗 (map2structure) | 11.311µs  | 25.776µs  | 234.465µs  | 1.128874ms  | 18.994346ms  | 144.340513ms |

* 基本结论

  * 呈线性增长
  * 曲线救国耗时更多, 几乎差一个量级
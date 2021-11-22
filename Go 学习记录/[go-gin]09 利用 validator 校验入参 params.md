# 利用validator第三方包校验入参 params

[TOC]

对于 api 接口, 入参校验是一定要有的, 很多人的做法是每一个入参单独校验, 造成大量代码浪费, 因此借助或者自制统一的校验入参来代替, 好处: 定义入参方便, 便于理解

## validator使用

```go
import "github.com/go-playground/validator/v10"

type GetProductListReq struct {
	ProductCode string `json:"product_code" validate:"min=2"`
	Page        int64  `json:"page" validate:"min=1"`
	Size        int64  `json:"size"`
	OrderBy     string `json:"order_by"`
}

var payload = &GetProductListReq{}

validate := validator.New()
err := validate.Struct(s)
```



## validator 文档

[详见](https://github.com/go-playground/validator)

[详见 2](https://darjun.github.io/2020/04/04/godailylib/validator/)

## 封装 map->struct(validator)

```go
// Unmarshal 参数解析到struct
func Unmarshal(kv map[string]interface{}, s interface{}) error {
	// 不做 s 类型判断主要是底层已经完成
	return map2struct.Unmarshal(kv, s)
}

// Validate 利用 validator 校验参数
func Validate(s interface{}) error {
	// 不做 s 类型判断主要是底层已经完成
	validate := validator.New()
	return validate.Struct(s)
}

// CheckParams 参数解析以及校验
func CheckParams(kv map[string]interface{}, s interface{}) (err error) {

	if err = Unmarshal(kv, s); err != nil {
		return err
	}
	if err = Validate(s); err != nil {
		return err
	}
	return nil
}
```

### Go-gin带有 validator 参数校验

```go
type SaveCodeReq struct {
	ProjectCode string `form:"project_code" binding:"gt=5,lte=128"`
	PointCode   string `form:"point_code" binding:"lte=128"`
	Code        int64  `form:"code"`
	Msg         string `form:"msg" binding:"required,lte=128"`
	RecoverMsg  string `form:"recover_msg" binding:"required,lte=128"`
}

func SaveCode(c *gin.Context) {
	var payload = &payloads.SaveCodeReq{}
	if err := c.ShouldBindJSON(payload); err != nil {
		payloads.HandleResp(c, err)
		return
	}
	Code, err := logic.SaveCode(c, payload)
	payloads.HandleResp(c, err, Code)
	return
}
```

* ShouldBindJSON、BindJSON等相关 json 操作,  tag 使用 json 标记
* ShouldBindQuery、ShouldBindForm... 表单形式以及 query等操作, tag 使用 form 标记
* ShouldBindUri 操作,  tag 使用 uri 标记
* 使用 binding替换 validate


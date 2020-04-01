# interface{}类型判断

[TOC]

在使用 go 开发过程中, 为了实现一些公用的方法, 在参数类型要求上不做规定. 所以这时候引入了 `interface{}`类型.

>  **interface{} 表示可以接纳所有的类型**

但是在程序中, 有传递, 就回有接收方, 此时, 接收方需要根据参数类型做转换和不同的操作; 

针对 传递参数为 **自定义的 struct** 也是合适的

## 断言转化

```go
//  变量 x 为 interface{}
if v, ok := x.(string) ; ok{
  // 此时 v 就是 字符串string类型; 可以对其做字符串操作
} else if v, ok := x.(int) ; ok{
  // 此时 v 就是 整数 int类型; 可以对其做字符串操作
} ...

// 直接断言, 不判断是否断言成功 --------> 很重要
v := x.(string) // 此时, 如果断言失败, 则为断言类型的默认值

```

断言的类型越多, if else 也多, 对于 有 强迫症 的程序员来说, switch 是最好的选择

## switch 一次断言, 多次使用

```go
//  变量 x 为 interface{}
switch v := x.(type) {
  case string:
  	// 此时 v 就是 字符串string类型; 可以对其做字符串操作
  case int:
	  // 此时 v 就是 整数 int类型; 可以对其做字符串操作
  ...
  default:
  	// 错误操作
}
```

* **强调 `x.(type)`只能配合 `switch` 使用, 其他地方都不能使用**


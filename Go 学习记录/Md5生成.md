# Golang 中 md5生成方式

[TOC]

## 方式一

```go
import "crypto/md5"
func md5V1(str string) string  {
    h := md5.New()
    h.Write([]byte(str))
    return hex.EncodeToString(h.Sum(nil))
}
```

## 方式二

```go
import "crypto/md5"
func md5V2(str string) string {
  has := md5.Sum([]byte(str))
  md5str := fmt.Sprintf("%x", has)
  return md5str
}
```

## 方式三

```go
func md5V3(str string) string {
  h := md5.New()
  io.WriteString(h, str)
  md5str := fmt.Sprintf("%x", h.Sum(nil))
  return md5str
}
```


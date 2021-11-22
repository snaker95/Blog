# string 索引和切片获取到的值类型



```go
import (
    "fmt"
)
str := "bac你"
for i, v := range str {
    fmt.Printf("for index is type %v, %T\n", str[i], str[i])
    fmt.Printf("for slice is type %v, %T\n", str[i:i+1], str[i:i+1])
    fmt.Printf("v is type %v, %T\n", v, v)
    fmt.Println()
    
}
```



```text
for index is type 98, uint8
for slice is type b, string
v is type 98, int32

for index is type 97, uint8
for slice is type a, string
v is type 97, int32

for index is type 99, uint8
for slice is type c, string
v is type 99, int32

for index is type 228, uint8
for slice is type �, string
v is type 20320, int32
```

### 结论:

对于字符串来说

* 索引获取,获取都得值为一个字节, 类型是 uint8 => byte
* 切片获取, 获取到的是子字符串, 类型是 string
* 通过 for 循环遍历到的值, 是一个 ascii 码, 是 int32 => rune
# array 申明

## 方式一 `[10]int{0,1,2,3,4,5,6,7,8,9}`

```go
array1 := [10]int{0,1,2,3,4,5,6,7,8,9}
for i := 0; i< 9; i++ {
  fmt.Println(array1[i])
 }
```

## 方式二 `[...]int{0,1,2,3,4,5,6,7,8,9}`

```go
// 无指定长度
array2 := [...]int{0,1,2,3,4,5,6,7,8,9}
fmt.Printf("the length of array2 is %d", len(array2))
```

## 方式三 `var xxx [10]type`

```go
var slice3 [10]int
for i := 0; i < 9; i++ {
  slice3[i] = i
}
```

* 主要 array 申明时如果指定长度, 则都会有对应类型的默认值
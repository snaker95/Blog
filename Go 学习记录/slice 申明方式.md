# slice 申明



## 方式一 `make([]type,len,cap)`

```go
slice1 := make([]int, 0, 9)
for i := 0; i < 9; i++ {
      slice1 = append(slice1, i)
 }
```

## 方式二 `make([]type,len)`

```go
// cap 隐式申明为 9
slice2 := make([]int, 9)
for i := 0; i < 9; i++ {
    slice2[i] = i 
} 
```

## 方式三 `var xxx []type`

```go
var slice3 []int
for i := 0; i < 9; i++ {
  slice3 = append(slice3, i)
}
```

* **小技巧**

* 2 个 slice 合并

* ```go
  newSlice := append(slice1, slice2...)
  ```

## **结论：**

方式一，在初始化时，申请cap大小的数组空间，每添加一个元素，len会变长，cap不变，适应于使用前已知slice长度的情况

方式二，在初始化时，申请cap大小的数组空间，每添加一个元素，len和cap都不变，适应于使用前已知slice长度的情况，（注意：方式二不能用append，否则append的数据，不会从第一个元素开始添加，而是会添加到slice的最末尾）

方式三，在初使化时，申请空间为0，根据使用情况动态扩充空间大小， 每添加一个元素，len和cap都会变 ，适应于使用前未知数组长度的情况

**性能：**方式一和方式二差不多，方式三，性能较差。


相关测试代码

slice_test.go 代码

```go
package main

import (
	"testing"
)

func TestSliceLenCap(t *testing.T) {
	slice1 := make([]int, 0, 9)
	t.Log("方式一：")
	t.Log("slice1 := make([]int, 0, 9)")
	t.Log("slice1 = append(slice1, i)")
	for i := 0; i < 9; i++ {
		slice1 = append(slice1, i)
		t.Log("slice1 len=", len(slice1), " ,cap=", cap(slice1))
	}
	t.Log(slice1)
}

func TestSliceCap(t *testing.T) {
	slice2 := make([]int, 9)
	t.Log("方式二：")
	t.Log("slice2 := make([]int, 9)")
	t.Log("slice2[i] = i")
	for i := 0; i < 9; i++ {
		slice2[i] = i
		t.Log("slice2 len=", len(slice2), " ,cap=", cap(slice2))
	}
	t.Log(slice2)
}

func TestSliceVar(t *testing.T) {
	var slice3 []int
	t.Log("方式三：")
	t.Log("var slice3 []int")
	t.Log("slice3 = append(slice3, i)")
	for i := 0; i < 9; i++ {
		slice3 = append(slice3, i)
		t.Log("slice3 len=", len(slice3), " ,cap=", cap(slice3))
	}
	t.Log(slice3)
}
```




首先做一下 go test查看输出的len 和cap 

* `go test -v`

* ```shell
  === RUN   TestSliceLenCap
  --- PASS: TestSliceLenCap (0.00s)
      slice_base_test.go:9: 方式一：
      slice_base_test.go:10: slice1 := make([]int, 0, 9)
      slice_base_test.go:11: slice1 = append(slice1, i)
      slice_base_test.go:14: slice1 len= 1  ,cap= 9
      slice_base_test.go:14: slice1 len= 2  ,cap= 9
      slice_base_test.go:14: slice1 len= 3  ,cap= 9
      slice_base_test.go:14: slice1 len= 4  ,cap= 9
      slice_base_test.go:14: slice1 len= 5  ,cap= 9
      slice_base_test.go:14: slice1 len= 6  ,cap= 9
      slice_base_test.go:14: slice1 len= 7  ,cap= 9
      slice_base_test.go:14: slice1 len= 8  ,cap= 9
      slice_base_test.go:14: slice1 len= 9  ,cap= 9
      slice_base_test.go:16: [0 1 2 3 4 5 6 7 8]
  === RUN   TestSliceCap
  --- PASS: TestSliceCap (0.00s)
      slice_base_test.go:21: 方式二：
      slice_base_test.go:22: slice2 := make([]int, 9)
      slice_base_test.go:23: slice2[i] = i
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:26: slice2 len= 9  ,cap= 9
      slice_base_test.go:28: [0 1 2 3 4 5 6 7 8]
  === RUN   TestSliceVar
  --- PASS: TestSliceVar (0.00s)
      slice_base_test.go:33: 方式三：
      slice_base_test.go:34: var slice3 []int
      slice_base_test.go:35: slice3 = append(slice3, i)
      slice_base_test.go:38: slice3 len= 1  ,cap= 1
      slice_base_test.go:38: slice3 len= 2  ,cap= 2
      slice_base_test.go:38: slice3 len= 3  ,cap= 4
      slice_base_test.go:38: slice3 len= 4  ,cap= 4
      slice_base_test.go:38: slice3 len= 5  ,cap= 8
      slice_base_test.go:38: slice3 len= 6  ,cap= 8
      slice_base_test.go:38: slice3 len= 7  ,cap= 8
      slice_base_test.go:38: slice3 len= 8  ,cap= 8
      slice_base_test.go:38: slice3 len= 9  ,cap= 16
      slice_base_test.go:40: [0 1 2 3 4 5 6 7 8]
  ```



然后用go test 的基准测试测试一下性能对比：

slice_benchmark_test.go 代码：

```go
package main

import (
	"testing"
)

var (
	num = 2
)

func BenchmarkSliceLenCap(b *testing.B) {
	slice1 := make([]int, 0, b.N/num)
	b.ResetTimer()
	for i := 0; i < b.N/num; i++ {
		slice1 = append(slice1, i)
	}
}

func BenchmarkSliceCap(b *testing.B) {
	slice2 := make([]int, b.N/num)
	b.ResetTimer()
	for i := 0; i < b.N/num; i++ {
		slice2[i] = i
	}
}
func BenchmarkSliceVar(b *testing.B) {
	var slice3 []int
	b.ResetTimer()
	for i := 0; i < b.N/num; i++ {
		slice3 = append(slice3, i)
	}
}
```

执行基准测试命令，

结果 ：

go test -bench=. -run=none

```shell
goos: darwin
goarch: amd64
pkg: exercise/base
BenchmarkSliceLenCap-4          233721600                5.10 ns/op
BenchmarkSliceCap-4             243148328                4.96 ns/op
BenchmarkSliceVar-4             126561564               12.5 ns/op
PASS
```


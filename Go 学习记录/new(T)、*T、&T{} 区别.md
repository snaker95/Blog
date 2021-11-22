```go
func main() {
	var vM *MyS
	nM := new(MyS)
	m := &MyS{}
	// 1. 打印角度
	fmt.Printf("vm = %T, %+v\n", vM, vM)
	fmt.Printf("nM = %T, %+v\n", nM, nM)
	fmt.Printf("m = %T, %+v\n", m, m)
	// 2. 赋值方式
	// vM.Name = "vM" // panic
	nM.Name = "nM"
	m.Name = "m"
	fmt.Printf("vm = %T, %+v\n", vM, vM)
	fmt.Printf("nM = %T, %+v\n", nM, nM)
	fmt.Printf("m = %T, %+v\n", m, m)
}

/**
vm = *main.MyS, <nil>
nM = *main.MyS, &{Name:}
m = *main.MyS, &{Name:}
vm = *main.MyS, <nil>
nM = *main.MyS, &{Name:nM}
m = *main.MyS, &{Name:m}
**/


```

1. new(T) 和 &T{} 是等效的,都分配一个零T并返回一个指向该已分配内存的指针, 在 google 中有人提到, 对内建类型支持的问题有别, 
2. *T 不能直接内部属性赋值, 会产生 panic 错误, 所有对 \*T仅申明类型, 未开辟内存空间


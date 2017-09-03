Go Slices: usage and internals
====

Arrays
----
go语言中数组是值类型，因此当做参数或者赋值的时候会引起拷贝，有以下初始化数组的方式：
```
b := [2]string{"Penn", "Teller"}
//编译器会计算数组的大小
b := [...]string{"Penn", "Teller"

```

Slices
----
切片可以类似于数组一样的初始化：
`letters := []string{"a", "b", "c", "d"}`
也可以通过内置的`make`函数创建，这时创建的切片会有切片头结构指向底层的数组：
``` 
func make([]T, len, cap) []T

var s []byte
s = make([]byte, 5, 5)
// s == []byte{0, 0, 0, 0, 0}
```
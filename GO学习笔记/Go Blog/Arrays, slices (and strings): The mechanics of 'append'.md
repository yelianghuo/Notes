Arrays, slices (and strings): The mechanics of 'append'
=========

数组是所有程序语言的共性，它解决如下几个问题：
1. 固定大小还是可变大小
2. 大小是否为数组类型的一部分
3. 多重数组的如何定义
4. 空数组的意义
对于以上问题的解决将决定数组的地位。

Array
----
数组在go程序中很少见，是因为数组的大小融合在数组类型中，这就限制了它的表现力
**声明**  
    `var buffer [256]byte`
数组在矩阵中还有一定的应用，但是另一个更加重要的作用是作为切片的底层存储

Slices: The slice header
------
切片本身不是数组但是它可以用来记录数组的一块连续区域
考虑到上面数组的声明，如下用`slice`来描述下标100到150的数组
    `var slice []byte = buffer[100:150]`
上述被数组来初始化的字节切片成为缓冲区，但是通常情况下，我们都会省掉类型，如下
    `var slice = buffer[100:150]`
我们还可以用更加简短的声明：
    `slice := buffer[100:150]`   
切片变量到底是什么？我们可以将切片当做有两个元素的结构，一个长度字段和一个指向数组的指针：
``` 
type sliceHeader struct {
    Length        int
    ZerothElement *byte
}

slice := sliceHeader{
    Length:        50,
    ZerothElement: &buffer[100],
}
```
这在将切片当做函数实参时会有影响。

Passing slices to functions
------
切片本身是个变量，但是包含一个指针字段和长度字段，这很重要。
当我们将切片当做形参时，会传递一个切片头的拷贝，考虑如下例子：
``` 
func AddOneToEachElement(slice []byte) {
    for i := range slice {
        slice[i]++
    }
}

func main() {
    slice := buffer[10:20]
    for i := 0; i < len(slice); i++ {
        slice[i] = byte(i)
    }
    fmt.Println("before", slice)
    AddOneToEachElement(slice)
    fmt.Println("after", slice)
}

result：
before [0 1 2 3 4 5 6 7 8 9]
after [1 2 3 4 5 6 7 8 9 10]
```
切片在这里是传值，但是切片头包含指向切片底层数组的指针，因此在切片拷贝的形参中会指向实际的数组地址，因此在上述函数调用中
函数返回之后的源切片会改变。
通过下例可以验证切片是传值：
``` 
func SubtractOneFromLength(slice []byte) []byte {
    slice = slice[0 : len(slice)-1]
    return slice
}

func main() {
    fmt.Println("Before: len(slice) =", len(slice))
    newSlice := SubtractOneFromLength(slice)
    fmt.Println("After:  len(slice) =", len(slice))
    fmt.Println("After:  len(newSlice) =", len(newSlice))
}

result：
Before: len(slice) = 50
After:  len(slice) = 50
After:  len(newSlice) = 49
```
这里可以发现切片头形参并没有在函数调用完之后改变，因为slice变量虽然在内部在次切片，但是这仅仅改变的是长度字段，而由于是传值，所以函数调用完之后slice
保持远样。因此我们想要改变切片参数的话，最好将切片当做返回值

Pointers to slices: Method receivers
----
另一种改变切片头的方法是将形参声明为切片头的指针，如下：
``` 
func PtrSubtractOneFromLength(slicePtr *[]byte) {
    slice := *slicePtr
    *slicePtr = slice[0 : len(slice)-1]
}

func main() {
    fmt.Println("Before: len(slice) =", len(slice))
    PtrSubtractOneFromLength(&slice)
    fmt.Println("After:  len(slice) =", len(slice))
}
```
我们还可以给切片定义一个方法来改变切片头：
``` 
type path []byte

func (p *path) TruncateAtFinalSlash() {
    i := bytes.LastIndex(*p, []byte("/"))
    if i >= 0 {
        *p = (*p)[0:i]
    }
}

func main() {
    pathName := path("/usr/bin/tso") // Conversion from string to path.
    pathName.TruncateAtFinalSlash()
    fmt.Printf("%s\n", pathName)
}
```
**总结**：
    
    这里多种改变切片头的方法其实是针对于改变切片头中的长度字段还是指针字段指向的值，由于长度字段是一般类型，因此如果我们要改变长度字段必须用返回值或者
    指针的方法

Capacity
---
``` 
func Extend(slice []int, element int) []int {
    n := len(slice)
    slice = slice[0 : n+1]
    slice[n] = element
    return slice
}

func main() {
    var iBuffer [10]int
    slice := iBuffer[0:0]
    for i := 0; i < 20; i++ {
        slice = Extend(slice, i)
        fmt.Println(slice)
    }
}

result：

[0]
[0 1]
[0 1 2]
[0 1 2 3]
[0 1 2 3 4]
[0 1 2 3 4 5]
[0 1 2 3 4 5 6]
[0 1 2 3 4 5 6 7]
[0 1 2 3 4 5 6 7 8]
[0 1 2 3 4 5 6 7 8 9]
panic: runtime error: slice bounds out of range

goroutine 1 [running]:
main.main()
	/tmp/sandbox457722027/main.go:27 +0x180
```
通过返回值的方法来修改切片，但是当切片遍历到底层数组的末尾时，会因为访问越界而报错。

除了长度和指针字段之外，切片头还有容量字段：
``` 
type sliceHeader struct {
    Length        int
    Capacity      int
    ZerothElement *byte
}
```
它记录底层数组实际有多少空间，这个字段用来防止切片越界

Make
----
如果我们想要增大切片的容量该怎么办？我们没有办法，因为我们在定义的时候就已经限制了大小。但是我们可以通过重新分配一个数组来实现这样的需求。
我们可以通过内置`new `函数来分配数组然后在切片，但是我们可以用`make`函数来代替上面两部：
``` 
slice := make([]int, 10, 15)
fmt.Printf("len: %d, cap: %d\n", len(slice), cap(slice))

result：
len: 10, cap: 15
```
下述方法将容量扩大2倍：
```  
slice := make([]int, 10, 15)
fmt.Printf("len: %d, cap: %d\n", len(slice), cap(slice))
newSlice := make([]int, len(slice), 2*cap(slice))
for i := range slice {
    newSlice[i] = slice[i]
}
slice = newSlice
fmt.Printf("len: %d, cap: %d\n", len(slice), cap(slice))

result：

len: 10, cap: 15
len: 10, cap: 30
```

Copy
-----
上述将容量扩大两倍的时候，通过一个循环来将老的数据拷贝到新的切片中，Go有内置的`Copy`函数可以更加完美的完成这个工作：
``` 
ewSlice := make([]int, len(slice), 2*cap(slice))
copy(newSlice, slice)
```
它会拷贝两个切片中长度最小的部分，返回拷贝的数量，该函数在两个切片有重叠时，依然会起作用。

Append: An example
----
以下`extend`函数避免了在容量不足时的自动扩容：
``` 
func Extend(slice []int, element int) []int {
    n := len(slice)
    if n == cap(slice) {
        // Slice is full; must grow.
        // We double its size and add 1, so if the size is zero we still grow.
        newSlice := make([]int, len(slice), 2*len(slice)+1)
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0 : n+1]
    slice[n] = element
    return slice
}
```
我们通过返回值来获得新的切片,由该自动扩容的`extend`函数可以得出`append`函数，`append`函数支持一些性往切片中插入多个值：
``` 
func Append(slice []int, items ...int) []int {
    for _, item := range items {
        slice = Extend(slice, item)
    }
    return slice
}

slice := []int{0, 1, 2, 3, 4}
fmt.Println(slice)
slice = Append(slice, 5, 6, 7, 8)
fmt.Println(slice)
 
result:
[0 1 2 3 4]
[0 1 2 3 4 5 6 7 8]   
```
在上述样例中另一个新特性是我们可以通过复合的文本在**花括号中直接初始化切片**：  
`slice := []int{0, 1, 2, 3, 4}`
我们不仅仅可以append元素，还可以直接用`...`来导入切片：
``` 
slice1 := []int{0, 1, 2, 3, 4}
slice2 := []int{55, 66, 77}
fmt.Println(slice1)
slice1 = Append(slice1, slice2...) // The '...' is essential!
fmt.Println(slice1)

result：
[0 1 2 3 4]
[0 1 2 3 4 55 66 77]

```
Append: The built-in function
-----
上述append方法是针对int类型的切片，go内置的append是支持任何类型的，我们必须保存append的返回值，否则go的编译器会报错。

Nil
---
nil切片：
```
sliceHeader{
    Length:        0,
    Capacity:      0,
    ZerothElement: nil,
}
```
不仅仅长度为0，而且指针也为nil，如下切片：
`array[0:0]`
虽然说长度为0，但是指针不为nil，因此它不为nil切片。
因此我们得明确，一个空的切片可以增长，但是一个nil切片底层是没有数组的，永远不可以增长，append方法可以作用于一个nil slice，因为append会检查长度字段然后
自动扩容

Strings
---
字符串实际上是一个**只读的字节切片**，没有容量字段。
我们可以通过下标索引：
` slash := "/usr/ken"[0] // yields the byte value '/'.`
我们可以切片获取字符串的子集：
`usr := "/usr/ken"[0:4] // yields the string "/usr"`
因此我们可以通过字节切片来创建string
`str := string(slice)`
相反的：
`slice := []byte(usr)`






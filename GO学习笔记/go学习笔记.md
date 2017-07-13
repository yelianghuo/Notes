此文记录go学习的点点滴滴

# 基础学习

**4中引用类型**
    
    * 数组切片
    * map       
    * channel
    * 接口
**字符类型**

    * byte(uint8的别名):代表UTF-8的单个字节的值；
    * rune:代表单个UNICODE字符
    
    
   
   
  b, ok := rd.(*Reader)
  ------
    Is it already a Reader?
	```
	For an expression x of interface type and a type T, the primary expression x.(T) 
	asserts that x is not nil and that the value stored in x is of type T. The notation x.(T)
	is called a type assertion.
	```
	
	
  log
  ----
	
	对于 log.Fatal 接口，会先将日志内容打印到标准输出，接着调用系统的 os.exit(1) 接口，退出程序并返回状态 1 。但是有一点需要注意，由于是直接调用系统接口退出，defer函数不会被调用，
	
	可见在Panic之后声明的defer是不会执行的
	
### channel有缓冲 与 无缓冲 的重要区别
  
  好文章
    
    https://segmentfault.com/a/1190000006261218
    http://hustcat.github.io/channel/
    
  c1 := make(chan int)
  c2 := make(chan int, 1)
  c1: 无缓冲的 不仅仅是 向 c1 通道放 1 而是 一直要有别的携程 <-c1 接手了 这个参数，那么c1<-1才会继续下去，要不然就一直阻塞着
  c2: 而 c2<-1 则不会阻塞，因为缓冲大小是1 （其实是缓冲大小为0）只有当 放第二个值的时候 第一个还没被人拿走，这时候才会阻塞。
  
### range作用于channel：
    
  for in := range channel {}
  range channel产生的迭代值为Channel中发送的值，它会一直迭代直到channel被关闭

### 指针对象的方法

```
package main

import (
    "fmt"
)

type IFace interface {
    SetSomeField(newValue string)
    GetSomeField() string
}

type Implementation struct {
    someField string
}    

func (i *Implementation) GetSomeField() string {
    return i.someField
}

func (i *Implementation) SetSomeField(newValue string) {
    i.someField = newValue
}

func Create() *Implementation {
    return &Implementation{someField: "Hello"}
}

func main() {
    var a IFace
    a = Create()
    a.SetSomeField("World")
    fmt.Println(a.GetSomeField())
}

```
  
  
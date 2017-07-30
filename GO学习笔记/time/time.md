分析time包里面的函数用法及功能
========

前言
----
* 本文章只是针对自己在工作学习中遇到的实际问题，然后探究相关知识点得出的结论，仅供参考。

### `time.format()`
* 功能： 格式化时间的输出
* 测试代码：
```
    fmt.Println(time.Now().Format("2006-01-02-15-04"))
    
    output:
      2017-07-26-20-11
```

* 结论: 要得到格式化的输出时间，就必须传入一个参考时间，官方给出的是:`Mon Jan 2 15:04:05 -0700 MST 2006`该问题是由于我在探究`go seelog`包时，延伸学习到的知识，`seelog`包中xml配置文件会涉及到该`format`知识，`seelog`相关知识可以参考我的seelog文章

* go sdk中给出的英文解释 :
```// Format returns a textual representation of the time value formatted
// according to layout, which defines the format by showing how the reference
// time, defined to be
//	Mon Jan 2 15:04:05 -0700 MST 2006
```

### `time.After(d Duration)` 
* 函数定义: `func After(d Duration) <-chan Time`,返回只读的channel，
* 功能: 延时d时间之后，将当前时间作为数据放在只读的channel返回值返回
* 应用场景: 定时作用
* 错误的用法：  
```
func h_time() {
	c := make(chan int, 1)
	defer close(c)
	h_flag := false
	for i := 1; i < 100000000; i++ {
		c <- i
		select {
		case m := <-c:
			fmt.Println(m)
		case tt := <-time.After(time.Second):
			fmt.Println("time out: ", tt)
			h_flag = true
		}
		if h_flag == true {
			break
		}
	}
}
```
 上述例子程序会把整个for循环执行完才会终止，因为在select中的case2会每次都新建一个timer，然后1秒之后返回，
 因为每次都是新建timer对象，因此程序在case2根本就读不到数据，最终执行完for循环结束。
 
* 正确的用法:

```
func h_time() {
	c := make(chan int, 1)
	defer close(c)
	time_chan := time.After(time.Second)
	h_flag := false
	for i := 1; i < 100000000; i++ {
		c <- i
		select {
		case m := <-c:
			fmt.Println(m)
		case tt := <- time_chan:
			fmt.Println("time out: ", tt)
			h_flag = true
		}
		if h_flag == true {
			break
		}
	}
}
```

### `time.Tick(d Duration)`

* 函数定义: func Tick(d Duration) <-chan Time
* 功能: 每隔d时间间隔就在只读channel返回值中返回当前时间，原理和`time.after()类似









    
    
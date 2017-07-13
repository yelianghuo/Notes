# fmt
   
 ## 格式化 
    
    * %v	相应值的默认格式。在打印结构体时，“加号”标记（%+v）会添加字段名
    * %#v	相应值的Go语法表示
   *eg:* 
   ```
    type test struct {
	    name string
	    age int
    }
    
    func main() {
        i := test{"hello", 25}
        fmt.Printf("%v\n",i)
        fmt.Printf("%#v",i)
    }
   ``` 
   *output:*
   ```
   {hello 25}
    main.test{name:"hello", age:25}

   ```
    
   
    * %T	相应值的类型的Go语法表示
    %t	单词 true 或 false。
Declaration and initialization
------------
go的map类型类似于`map[KeyType]ValueType`，`var m map[string]int：`m是键为字符串值为int，map同指针和切片一样是引用类型，
因此上述m值为nil，因此尝试往m中写数据会导致panic，我们得用内置的make函数：`m = make(map[string]int)`，make函数会分配内存、初始化
map结构，

Working with maps
-----
设定"route"的值为66:
``` 
m["route"] = 66
// 取值
i := m["route"]

// 取值时，如果键值不存在，就会返回0值
j := m["root"]
// j == 0

// 删除map中某个键值对，如果键不存在则不做任何事情
delete(m, "route")

// 下例获取值的方法如果key不存在，则i为0，ok为false，这样可以避免i为0的异议。
i, ok := m["route"]

// 遍历map
for key, value := range m {
    fmt.Println("Key:", key, "Value:", value)
}

// 初始化map
commits := map[string]int{
    "rsc": 3711,
    "r":   2138,
    "gri": 1908,
    "adg": 912,
}

// 初始化空map，和make一样
m = map[string]int{}

```

Exploiting zero values
-----
索引map，当键不存在时返回0值是十分有用的，以下例子利用默认返回的0值来判断链表是否为环形
``` 
type Node struct {
    Next  *Node
    Value interface{}
}
var first *Node

visited := make(map[*Node]bool)
for n := first; n != nil; n = n.Next {
    if visited[n] {
        fmt.Println("cycle detected")
        break
    }
    visited[n] = true
    fmt.Println(n.Value)
}
```




* `INCR`: 将某个整数值加1,为原子操作  
``` 
x = GET count
x = x + 1
SET count x
```
* `EXPIRE`: 设置某个key存活的时间   
``` 
SET resource:lock "Redis Demo"
EXPIRE resource:lock 120
```
* `TTL`: 查看某个key被删除的时间，返回值(-2)代表已经被删除了，永远不存在；返回值(-1)代表该key永久不失效
``` 
TTL resource:lock => -2
```

**`LIST`**: 拥有`RPUSH, LPUSH, LLEN, LRANGE, LPOP, and RPOP`这些交互的操作
<不支持下标操作？？ 如何直接查看一个list的结构>
``` 
RPUSH friends "Alice"
RPUSH friends "Bob"
LPUSH friends "Sam"

LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"
LRANGE friends 0 1 => 1) "Sam", 2) "Alice"
LRANGE friends 1 2 => 1) "Alice", 2) "Bob"

LLEN friends => 3

LPOP friends => "Sam"

RPOP friends => "Bob"
```
**SET**: 交互式的操作命令有`SADD, SREM, SISMEMBER, SMEMBERS and SUNION`

``` 
# add
SADD superpowers "flight"
SADD superpowers "x-ray vision"
SADD superpowers "reflexes"

# remove 
SREM superpowers "reflexes"

# ismenber 1 mean in set, 0 means not in 
SISMEMBER superpowers "flight" => 1
SISMEMBER superpowers "reflexes" => 0

# list all member in set
SMEMBERS superpowers => 1) "flight", 2) "x-ray vision"
    
# combine two or more sets

```




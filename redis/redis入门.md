redis入门基本操作
========

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
SUNION SET1 SET2
```

**sort set**: 有序set,相关命令`ZADD ZRANGE`
``` 
ZADD hackers 1940 "Alan Kay"
ZADD hackers 1906 "Grace Hopper"
ZADD hackers 1953 "Richard Stallman"
ZADD hackers 1965 "Yukihiro Matsumoto"
ZADD hackers 1916 "Claude Shannon"
ZADD hackers 1969 "Linus Torvalds"
ZADD hackers 1957 "Sophie Wilson"ZADD hackers 1912 "Alan Turing"

ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) "Alan Kay", 3) "Richard Stallman"
```

**hashes**: 映射
``` 
HSET user:1000 name "John Smith"
HSET user:1000 email "john.smith@example.com"
HSET user:1000 password "s3cret"
HGETALL user:1000

HMSET user:1001 name "Mary Jones" password "hidden" email "mjones@example.com"
HGET user:1001 name => "Mary Jones"

# hash中的值支持incr操作
HSET user:1000 visits 10
HINCRBY user:1000 visits 1 => 11
HINCRBY user:1000 visits 10 => 21
HDEL user:1000 visits
HINCRBY user:1000 visits 1 => 1
```




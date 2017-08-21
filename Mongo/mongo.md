
##Basic conception
MongoDB是一个开源的文档型数据库，具有高性能，高可靠性、自动扩展的优势。
* Document: MongoDB中所有的记录都是文档，他是一个类似于map的键值结构，和json格式的数据雷同。值可以是文档或者数据，或者是这两者的任意结合。


* Collections collectin的概念等价于关系型数据库中的表，因此collection中存储的是一系列的文档，但是collection中文档的数据格式没必要一样，可以随意存储。

入門基本操作
--------
* db: 显示当前操作的数据库
* show dbs: 显示所有的数据库
* use db: 切换到制定数据库
* db.getSiblingDB("DbName"): 在当前的数据库下，允许操作其他数据库，但是当前的上下文还是当前数据库
        
        db = db.getSiblingDB('users')
        db.active.count()

* `DBQuery.shellBatchSize = 10` :可以修改`.find()`默认输出的条数，默认输出２０条

**创建新数据库的方法**  
当我们向集合中插入数据时，MongoDB自动创建数据库和集合
``` 
use myNewDatabase
db.myCollection.insertOne( { x: 1 } );
```
当我们的集合名字包含特殊字符时，比如说空格、连字符号或者以数字开头，我们可以用如下方法来替代：
``` 
db["3test"].find()
db.getCollection("3test").find()
```




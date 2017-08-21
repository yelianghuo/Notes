CURSOR
=====

`db.collection.find()`方法返回cursor对象，我们可以迭代该对象来访问document。在mongo shell中如果我们直接调用find()方法而不获取cursor的话，
cursor对象会自动迭代２０次然后在屏幕打印出来

手动迭代cursor
-----
``` 
var myCursor = db.users.find( { type: 2 } );

var myCursor = db.users.find( { type: 2 } );
var i = 0;
while (myCursor.hasNext() && i < 3) {
   print(tojson(myCursor.next()));
   i++;
}
```  
`print(tojson())`可以换成`printjson()`,我们还可以用cursor的foreach()方法来迭代所有的结果
``` 
var myCursor =  db.users.find( { type: 2 } );

myCursor.forEach(printjson);
```

索引迭代器
----
在mongo shell中我们可以用toArray()方法来迭代访问cursor,并且将document以数组返回，
``` 
var myCursor = db.inventory.find();
var documentArray = myCursor.toArray();
var myDocument = documentArray[3];
```
toArray()方法将cursor中返回的所有的document加载进RAM中，并且cursor结束迭代。
有些驱动器直接对cursor的下标索引`cursor[index]`，其实这等价于先调用toArray()方法，然后在索引下标，
``` 

var myCursor = db.users.find( { type: 2 } );
var myDocument = myCursor[1];
```
`myCursor[1]`等价于
``` 
myCursor.toArray() [1];
```

cursor的行为
-------

### 关闭不活跃的cursor

默认的，服务器会10分钟内没有被使用的cursor,在mongo shell中我们可以通过`cursor.noCursorTimeout()`来改变这个缺省设置
` var myCursor = db.users.find().noCursorTimeout();`
经过这样的设置之后我们必须手动关掉cursor `cursor.close() `或者该cursor迭代到结尾


cursor的隔离
------
当一个cursor被获取，返回document的时候，另一个操作可能在这个时候会介入，这时候另一个操作的写行为可能会导致当前cursor返回更多的document，
解决这个冲突可以参考` snapshot mode.`


cursor批量操作
------

mongo服务器会批量的返回查询的结果，批量返回的结果的总体大小不能超过最大的bson文档的大小（16M），改变这个缺省的设置可以通过` batchSize() and limit()`
但是这只可以让缺省的大小变小，而不可以调大。


cursor的相关信息
------

`db.serverStatus()`返回一些字段，包括cursor的相关信息

* 自上一次服务器重启后，超时的cursor的数量
* 通过`noCursorTimeout`设置用不超时的cursor的数量
* 固定打开的cursor数量
* 所有打开状态的cursor数量

`db.serverStatus().metrics.cursor`
result:
``` 
{
   "timedOut" : <number>
   "open" : {
      "noTimeout" : <number>,
      "pinned" : <number>,
      "total" : <number>
   }
}
```
















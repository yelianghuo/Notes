buffer讲解: https://mp.weixin.qq.com/s?__biz=MzU4NDQ4MzU5OA==&mid=2247483961&idx=1&sn=f67bef4c279e78043ff649b6b03fdcbc&chksm=fd985458caefdd4e3317ccbdb2d0a5a70a5024d3255eebf38183919ed9c25ade536017c0a6ba#rd
buffer jdk: file:///Users/yelianghuo/Downloads/docs/api/java/nio/ByteBuffer.html


* flip() 将写模式调整为读模式
* clear() compact()可以将读模式调整为写模式

#### clear() vs compact()

    clear方法将position设回0，limit设置成capacity的值；compact方法将position到limit之间的未读数据整体的移动到0开始的位置

代码示例：

```
 ByteBuffer byteBuffer = ByteBuffer.allocate(48);
 byteBuffer.clear();
 byteBuffer.put("abcs".getBytes());
 System.out.println(byteBuffer);
 System.out.println(new String(byteBuffer.array()));
 // 更换为读模式
 byteBuffer.flip();
 System.out.println(byteBuffer);
 System.out.println((char) byteBuffer.get()); // output: a
 System.out.println(byteBuffer);
 // 将position->limit之间的数据整体移动到0-(limit-position)的位置,更改读模式为写模式，这样就可以保留当前buffer中未读的数据，且向buffer中写数据是从（limit-postion）之后的位置开始，
 byteBuffer.compact();
 System.out.println(byteBuffer);
 byteBuffer.put("e".getBytes());
 System.out.println(byteBuffer);
 System.out.println((char)byteBuffer.get(0)); // output: b
 System.out.println((char)byteBuffer.get(1)); // output: c
```



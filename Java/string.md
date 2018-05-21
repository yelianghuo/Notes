


### ==
* 应用基本数据类型（byte,short,char,int,long,float,double,boolean）时，==操作符是比较他们的值，应用与复合类型时，比较的是他们在内存中的存放地址。

### new String()
* String s = newString("1")，生成了常量池中的“1” 和堆空间中的字符串对象。

### string.intern()
* 在jdk1.7以上， 该方法会先判断常量池中是否有该字符串，若没有则将常量池中该字符串索引到堆中的当前字符的地址，若有就返回常量池中该字符串的地址。jdk1.6中该方法是在常量池中单独
创建一个对象，然后在返回常量池中的地址。
```
String name = new String("ye") + new String("lianghuo");
System.out.println(name.intern() == name);
String s = "yelianghuo";
System.out.println(name ==s);

out: true, true


String s = "yelianghuo";
String name = new String("ye") + new String("lianghuo");
System.out.println(name.intern() == name);
//String s = "yelianghuo";
System.out.println(name ==s);
System.out.println(name == "yelianghuo");

out: false, false
```
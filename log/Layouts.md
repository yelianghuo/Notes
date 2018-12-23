Pattern Layout
=====

**总结**：通过阅读log4j2官方文档可知，在java开发中如下`PatternLayout`的配置应该可以基本满足记录日志的需求，同时具体较高的性能；至于其中的`(%F:%L)`是否有必要保留，有待进一步在实际使用过程中进行总结。
```
    <PatternLayout pattern="%d %t [%p] %c (%F:%L) %msg%n" />
```

   通过特定转化格式的字符串来配置layout，它的目标是以配置规则来格式化日志事件，然后返回结果。转化的格式类似于C语言中的printf函数，转化格式包含基础字符串和转换说明符；基础的字符串可以包括任意的字符串，也可以包括一些我们在编码里使用的转义符，包括" \t, \n, \r, \f, \\"等等；转换说明符以`%`开始，随后跟着可选的格式化标识符和转换字符，格式化标识符的作用包括指定输出的宽度、对齐方式和填充方法；而转换字符则用来表名输出数据的类型、分类、日期和线程名称等等。

   转化格式字符形如："%-5p [%t]: %m%n"，同时假设在项目中Log4j2配置文件中指定的是PatternLayout；如下的日记记录语句：
```
    Logger logger = LogManager.getLogger("MyLogger");
    logger.debug("Message 1");
    logger.warn("Message 2");
```
   日志记录输出如下：
```
   DEBUG [main]: Message 1
   WARN  [main]: Message 2
```
   在基础的文本字符串和转换说明符之间没有任何明确的分隔符，该layout的分析模块能知道不同功能字符之间的界限。在上面的例子中"%-5p"的作用是将log事件以5个字符的宽度进行左对齐输出。


Patterns
----

### 转换字符

Log4j2提供了如下转换字符:

**c{precision}或者
  logger{precision}**

    目标在于输出logging事件的名称，c/logger后面可以包含一个十进制的数值，用来减少logger名字的输出；若为正值，则名称从右边开始保留；若为负值，则从左边开始删除。该十进制数值还可以是非int类型的，这种情况感觉不适用，因此不作记录。
* %c{1}  Looger Name: "org.apache.commons.Foo"  Result: "Foo"
* %c{2}	 Looger Name: "org.apache.commons.Foo"  Result: "commons.Foo"
* %c{10} Looger Name: "org.apache.commons.Foo"  Result: "org.apache.commons.Foo"
* %c{-1} Looger Name: "org.apache.commons.Foo"  Result: "apache.commons.Foo"
* %c{-2} Looger Name: "org.apache.commons.Foo"  Result: "commons.Foo"
* %c{-10} Looger Name: "org.apache.commons.Foo"  Result: "org.apache.commons.Foo"

**C{precision}或者
   class{precision}**

    目标在于记录logger事件发生类的全限定性类名，和上面的转换符一样具有格式控制和precision的功能。不过产生日志记录发生时所在的类名是极其损耗性能的做法。todo：查看源码，为什么获取logger事件发生的类名会比较耗时。

**d{pattern}
  date{pattern}**

    输出logger事件发生的事件，其后的pattern格式可以指定输出事件的具体格式。这里推荐直接用`%d`，默认格式输出结果为`2012-11-02 14:34:02,123`。

**t
  tn
  thread
  threadName**

    输出logger事件所在的线程名称

**m
  msg
  message**

    输出logger事件中，我们传入的数据。

**n**

    该转换字符输出系统相关的行分隔符或字符，用该转换符可以达到与直接用换行符"\n", or "\r\n"同样的性能；但是用该换行符更加便捷。

**p|level{level=label, level=label, ...} p|level{length=n} p|level{lowerCase=true|false}**
    输出该logger事件的级别，若使用可选参数`level=label`的话，可以对默认的级别字符进行重命名，如`WARN=Warning`，则WARN级别的日志输出Warning；其他可选参数可以参考官网。


**F
  file**

    输出logger事件发生的文件名，比较影响性能。

**L
 line**

    输出logger事件发生所在的代码行，比较影响性能

**l
  location**
    输出logger事件发生的位置，该位置处决于JVM的实现，但是通常包括全限定性方法名 + 源码的文件名 + 和所在的代码行。比较影响性能。


### 格式化标识符
**对齐**
   格式化说明符作用就是控制转换字符结果输出的对齐方式、宽度...等等，这里以`%c{1}`举例稍微作少量记录；
```
%c{1}  Looger Name: "org.apache.commons.Foo"  Result Logger Name: "Foo"
```
* %1c{1}   Result Logger Name: "Foo"
* %2c{1}   Result Logger Name: "Foo"
* %4c{1}   Result Logger Name: " Foo"
* %10c{1}  Result Logger Name: "       Foo"

* %-1c{1}  Result Logger Name: "Foo"
* %-2c{1}  Result Logger Name: "Foo"
* %-4c{1}  Result Logger Name: "Foo "
* %-10c{1} Result Logger Name: "Foo       "

结论：转换字符前面的若没有数值或者数值为正数则进行右对齐，负数的话就进行左对齐。若数值小于输出结果的长度，就什么都不做，若大于输出结果的长度则用空格进行填充。












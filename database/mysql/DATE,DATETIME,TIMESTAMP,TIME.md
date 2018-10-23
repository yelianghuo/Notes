mysql中时间相关的字段详细介绍
=================

**背景**: 最近在工作中，处理了几个线上的问题，过程中涉及到了`mysql`中`timestamp datetime`这个两个字段的处理，记得当看到其中某个服务函数的参数对象中涉及到时间相关的字段时，当时内心感觉到有些惶恐，感觉写sql无从下手，不知道时间相关的字段该如何来表示。因此这里记录mysql中时间相关字段的处理方法。

mysql中`TIME, DATE, DATETIME, and TIMESTAMP`字段值的多种格式
---------------------------------------------------

先记录这几种时间相关字段在sql语句和java代码中的实际使用方式，稍后在对比它们的异同点。

### 字符串和数值类型的格式

**DATETIME and TIMESTAMP**

    DATETIME 和 TIMESTAMP在具体使用时(sql、java)有字符串和数值两种表现形式，详细看下文中的例子。

* 字符串形式：'YYYY-MM-DD HH:MM:SS' 和 'YY-MM-DD HH:MM:SS'这两种表现形式，mysql支持任何标点作为字符串格式中的分隔符，比如：'2012-12-31 11:30:45','2012/12/31 11 * 30 * 45', '2012 @ 12@31 11 ^ 30 ^ 45'在mysql中最终表现的效果一样
* 字符串格式也可以不使用分隔符，像'YYYYMMDDHHMMSS' 和 'YYMMDDHHMMSS'，比如，
'20070523091528' and '070523091528' 等同于 '2007-05-23 09:15:28'这种带分隔符的表现形式，但是mysql会将'071122129015' 这种数据当作错误数据。
* 这两种字段的date和time部分之间的分隔符可以用`T`来替换空格
* 数值形式： YYYYMMDDHHMMSS 或者 YYMMDDHHMMSS，mysql会自动的读取这种数值类型的时间数据，比如：19830905132800 和 830905132800 等同于 '1983-09-05 13:28:00'.

*sql实际测试例子：*
```
#1 建一张测试表，仅仅包含本文中涉及到的4种类型的字段（这里先忽略前两个字段，主要是验证后两个字段），在下面的sql实例中，年月日时分秒之间可以用上面介绍的任何标点符号进行代替。

CREATE TABLE `time_relate_test` (
  `c1` date,
  `c2` time ,
  `c3` datetime ,
  `c4` TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=latin1;


    ## 以字符格式的数据进行字段的插入

        insert into time_relate_test
        values('2018-09-18', '17:51:04', '2018-09-18 17:51:04', '2018-09-18 17:51:04')

        insert into time_relate_test
        values('18-09-18', '17:51:04', '18-09-18T17:51:04', '18-09-18T17:51:04')

    ## 以数值格式的数据进行字段的插入，这里
        insert into time_relate_test
        values(19980908, 175104, 19980908175104, 19980908175104)

        insert into time_relate_test
        values(980908, 175104, 980908175104, 980908175104)

----------
# 进行查询（重点），由于之前很少写sql，并且几乎没有写过时间相关字段的sql，因此这次在解决线上case的过程中，
刚开始感觉到恐惧的绝大部分原因是由此引起的。
    ## 这里以timestamp字段举例，由于上面介绍了有字符串和数值两种形式，因此这里直接写sql从数据库中获取数据

        select *
        from time_relate_test
        where c4 > 20000101010101

        select *
        from time_relate_test
        where c4 > 000101010101

        select *
        from time_relate_test
        where c4 > '00-01-01 01:01:01'

        select *
        from time_relate_test
        where c4 > '2000-01-01 01:01:01'
```
*Java代码中测试样例：*
```
todo
```

在mysql 5.6.4版本之后，DATETIME和TIMESTAMP字段的精度可以精确到微秒，比如：'2010-12-10 14:12:09.019473'，小数点之后就是精确到微秒的数据，由于当前工作很大可能不会涉及到微秒的精度，因此不对其作过多介绍，具体可参考[官方文档](https://dev.mysql.com/doc/refman/5.6/en/fractional-seconds.html)。

以上两个时间相关字段的具体数值的年份中可以不指定世纪，mysql中处理的逻辑如下：
* 年份在70-99就转化为1970-1999.
* 年份在00-69就转化为2000-2069.

**DATE and TIME**

    DATE加TIME字段可以看作DATETIME字段的组成部分，因此上面介绍的TIMESTAMP和DATE的规则其实都适用于DATE和TIME，
    同理TIME字段可以有小数部分。具体的sql实例可以参考上面中的例子。

* 字符串形式：'YYYY-MM-DD'/'YY-MM-DD'(DATE)和'HH:MM:SS', 'HH:MM', 'D HH:MM', 'D HH', or 'SS'(TIME)
* 数值形式：  YYYYMMDD or YYMMDD(DATE)和HHMMSS(TIME)。

对于字符串形式的时间值，像月份、日、时、分、秒等数据，如果可以用一位来表示，那就没必要用两位(填充0的方式)，比如：
'2015-6-9'和'2015-06-09'一样，'2015-10-30 1:2:3'和'2015-10-30 01:02:03'一样。

对于数值型的时间值，通常他们应该是6, 8, 12, or 14位等长度，如果是8 或者 14位的话，那么此数据就代表YYYYMMDD or YYYYMMDDHHMMSS，如果是6 or 12位，那么就是YYMMDD or YYMMDDHHMMSS这样的形式，也就是年份只用了两位来表示。如果数值型时间值不是上述的这些位数的话，比标准位数少就会在左端填充0进行对齐，如果位数多的话，mysql会报错。

如果数值型的数值8或者14位的话，mysql默认将前四位字符当作年份，否则的话mysql读取数值型的数值顺序是依次读取year, month, day, hour, minute, and second 。因此，我们的数据不应该少于6位，否则就会导致插入报错，比如用'9903'，来写插入或者查询，mysql视其为0值。


DATETIME, and TIMESTAMP 异同点
---------------------------
DATETIME包含DATE和TIME这两个部分，它表示的时间范围'1000-01-01 00:00:00' to '9999-12-31 23:59:59'，而TIMESTAMP表示的时间范围是'1970-01-01 00:00:01' UTC to '2038-01-19 03:14:07' UTC。同时TIMESTAMP的数据在存储时，会转换为UTC（世界统一时间），再取出的时候
又会转换成当前时区的时间。

**结论**:

    当遇到未知的问题时，静下心来，从问题的源头找原因。


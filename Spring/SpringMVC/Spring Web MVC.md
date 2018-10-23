WebApplicationContext has a link to the ServletContext and ServletContext it is associated with.
It is also bound to the ServletContext such that applications can use static methods on
RequestContextUtils to look up the WebApplicationContext if they need access to it.
 See Additional Capabilities of the ApplicationContext for more on the context hierarchy feature.

### Context Hierarchy(上下文的层级结构)

DispatcherServlet期望获得一个继承自ApplicationContext上下文的WebApplicationContext上下文，以此进行配置。WebApplicationContext上上下文
有ServletContext和ServletContext的索引。它也由于绑定了ServletContext，因此可以用RequestContextUtils工具类来查询自己。

对于大多数应用来说，有一个单独WebApplicationContext就足够了。但是我们也可以拥有一个根WebApplicationContext，它被所有子
WebApplicationContext所共享。查看[Additional Capabilities of the ApplicationContext](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#context-introduction)
来获取更多的消息。

> 区块引用
>> 嵌套引用

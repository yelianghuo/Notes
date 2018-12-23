
  虽然Log4j2 提供了接受String和Objects的Looger方法，但是最终这些入参都会被Message对象捕获然后发送给Log event。应用程序可以不用构造Messages，貌似将消息格式和参数通过Message对象传递给Log event是十分昂贵的操作，但测试用当今JVM测试表明，这些开销都很微小，尤其体现在把复杂的log任务封装在Message中，而不是只在应用程序中进行判断。


  考虑如下Logger输出语句，如果我们想要在日志中记录多个入参，那么我们必须增加许多object入参来进行输出，log2j源代码中最多只支持9个object入参，如果这样的话，代码将显得十分难看。
```
    logger.info("User {} has logged in using id {}", map.get("Name"), user.getId());
```
  我们可以通过Message对象来避免上述的冗余过程，通过委托Message对象的`getFormattedMessage`方法进行格式化输出。这种方案中，对于没有`toString`方法的对象也是十分有用的，我们可以在Message对象中对齐进行格式化输出。

```
    logger.info(new LoggedInMessage(map, user));
```

  Message对象的另一个好处是简化了Layouts的编写，请参考官方文档中的例子。

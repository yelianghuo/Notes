Static vs Non-Static Loggers
============================







   如下配置文件中，%logger的名字源自于创建该logger的类的全局限定名，而%class的名称则源自于logging方法调用发生类的全局限定名。

```
    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration status="error">
      <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
          <PatternLayout>
            <MarkerPatternSelector defaultPattern="%sn. %msg: Logger=%logger%n">
              <PatternMatch key="CLASS" pattern="%sn. %msg: Class=%class%n"/>
            </MarkerPatternSelector>
          </PatternLayout>
        </Console>
      </Appenders>
      <Loggers>
        <Root level="TRACE">
          <AppenderRef ref="Console" />
        </Root>
      </Loggers>
    </Configuration>
```
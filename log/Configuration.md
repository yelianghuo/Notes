Configuration
=============

Log4j2的配置分以下四个步骤完成：
1. 通过以XML，JSON，YAML或属性格式编写的配置文件。
2.


Additivity
----------
    在记录日志的过程中，可能我们想要某一个组件（类或者包）中的日志和其他地方记录方式不一样，比如说输出的日志级别、格式、位置不同，则我们可以通过定义特定的Logger来实现。

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <Configuration status="WARN">
       <Appenders>
           <Console name="Console" target="SYSTEM_OUT">
               <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
           </Console>
       </Appenders>

       <Loggers>
           <Logger name="log" level="TRACE" additivity="false">
               <AppenderRef ref="Console"/>
           </Logger>

           <Root level="ERROR">
               <AppenderRef ref="Console"/>
           </Root>
       </Loggers>
   </Configuration>
   ```
   上面配置文件中，log为包名，因此，在log包下的所有日志输出都会以`level="TRACE`，同时用`<AppenderRef ref="Console"/>`来进行格式化输出。而不在log包下的其他日志记录都会以root，日志级别为`level="ERROR"`进行输出。

   由于上述配置文件中，log继承了root，导致log会有两个Appender，因此加上additivity="false"来指定只用当前logger中定义的appender输出。

Automatic Reconfiguration
-------------------------
    当从根据文件进行配置时，Log4j支持自动检测配置文件的变化，并重新加载它们。通过在Configuration标签中设置属性`monitorInterval`为非零值来定义重新检测配置文件的时间间隔，不过重新检测必须由一个log事件或者log记录日志时才会被激活。下例配置的是30秒自动重新检测：
    ```
        <?xml version="1.0" encoding="UTF-8"?>
        <Configuration monitorInterval="30">
        ...
        </Configuration>
    ```

Configuring loggers
-------------------
    LoggerConfig是通过logger标签来进行配置的，logger标签必须有那么属性，同时经常会带有level和additivuty属性。level属性有TRACE, DEBUG, INFO, WARN, ERROR, ALL or OFF等多种选择；如果没有level属性的话，默认日志输出级别就为ERROR。每个LoggerConfig可以配置多个AppenderRef标签，当前logging时间发生时，appdenders将会被唤醒。
    每个配置文件必须有一个根logger，如果没有配置的话，log4j2就会定义一个level=ERROR的Console appender；根logger和其他logger具有以下两个不同点：

* 根looger没有name属性
* 根logger没有additivity属性，因为根logger没有父logger
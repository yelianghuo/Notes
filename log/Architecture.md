Architecture
============

![](Log4jClasses.jpg)
**核心概念:**

    程序使用log4j2的话必须从LogManager中获取到指定名称的Logger，LogManager会定位到合适的LoggerContext，然后从其中获取到Logger。如果必须实例化Logger的话，log4j2将会以配置文件(Congiguration)中的LoggerConfig(Logger子标签)为指导进行实例化，有以下三种实例化策略：a) 以指定名称的logger，b) 当前名称的父package名称的logger， c) 根logger，因此在Congiguration配置文件中，除非指定了特定logger，其包下的所有类都会以当前logger定义的格式进行格式化文件的记录；否则，其他包下的所有类文件都会以根logger进行格式化日志输出。

Logger Hierarchy
----------------

log4j2 API最大的优势在于它可以让你过滤某些log语句，同时让你可以有效的输出，也就是说logging语句是通过特定的条件进行分类的。Loggers和LoggerConfigs都是命名实体，Loggers的命名遵守如下规则：

    假设一个LoggerConfig的属性name="com.foo"，那么它是另一个LoggerConfig属性name="com.foo.Bar"的父亲，这里Logger之间的关系和java中类之间的关系很类似；同时这些继承规则，在某一个具体类中进行日志输出时，当前类默认会取以当前类的全局限定名取匹配LoggerConfig来进行logger的实例化。如下代码和配置文件示例所示，log包下的类在获取Logger的时候，会和配置文件中的相同标签(或者找父Logger)<Logger>属性name进行匹配，然后获取到具体的appender进行日志的输出。因此Bar中的logger实例化为log.bar，同时以level="TRACE"进行输出，其他包下的则以根looger level="ERROR"进行日志输出。


```
    <Loggers>
        <Logger name="log.Bar" level="TRACE" >
            <!--<AppenderRef ref="Console"/>-->
        </Logger>

        <Root level="ERROR">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>


    Bar.java

    package log;
    import org.apache.logging.log4j.LogManager;
    import org.apache.logging.log4j.Logger;

    public class Bar {
        static final Logger logger = LogManager.getLogger(Bar.class);
        public boolean doIt() {
            logger.entry();
            logger.error("Did it again!");
            return logger.exit(false);
        }
    }



```

Appender
--------

** Appender Additivity：**


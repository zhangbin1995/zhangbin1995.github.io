---
title: springboot-logback日志框架的使用
date: 2018-07-21 15:40:48
tags:
	- java
	- springboot
categories:
	- java
---

# 一 日志的简单介绍（可直接看第二部分）

> * 什么是日志框架
> * 日志框架的选择
> * Logback的使用和配置

### 什么是日志框架

> 是一套能实现日志输出的工具包

> 能够描述系统运行状态的所有时间都可以算作日志（用户下线，接口超时，数据库崩溃等等）

### 日志框架的能力

> * 定制输出目标
> * 定制输出格式
> * 携带上下文信息
> * 运行时选择性输出
> * 灵活的配置
> * 优异的性能

### 常见的日志框架

> JUL JCL Log4j Log4j2 Logback SLF4j jboss-logging

> * 日志门面：JCL SLF4j jboss-logging
> * 日志实现：Log4j Log4j2 JUL

> SLF4j Log4j Logback为同一个作者，实际上，logback是Log4j 2.0   Log4j2和Log4j并没有直接联系

**在springboot中我们选用的是SLF4j+Logback**

新建一个简单的测试类：

```
/**
 * Created by binzhang on 19/1/21.
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class LoggerTest {

    private final Logger logger = LoggerFactory.getLogger(LoggerTest.class);

    @Test
    public void test1(){
        logger.debug("debug...");
        logger.info("info...");
        logger.error("error...");
    }

}
```

运行结果：

![](/uploads/190121java2/1.png)

为什么只输出了info和error呢？

因为我们控制台打印的日志的级别默认是info，debug级别小于info故没有输出。

可以去Level类中查看级别（ctrl+o查找类，slf4j下的）源码：

Level.java

```
/**
 * 
 * @author ceki
 * @since 1.7.15
 */
public enum Level {

    ERROR(ERROR_INT, "ERROR"), WARN(WARN_INT, "WARN"), INFO(INFO_INT, "INFO"), DEBUG(DEBUG_INT, "DEBUG"), TRACE(TRACE_INT, "TRACE");

    private int levelInt;
    private String levelStr;

    Level(int i, String s) {
        levelInt = i;
        levelStr = s;
    }

    public int toInt() {
        return levelInt;
    }

    /**
     * Returns the string representation of this Level.
     */
    public String toString() {
        return levelStr;
    }
}
```

逐层向下可看到级别定义所在类LocationAwareLogger.java:

```
public interface LocationAwareLogger extends Logger {

    // these constants should be in EventContants. However, in order to preserve binary backward compatibility
    // we keep these constants here
    final public int TRACE_INT = 00;
    final public int DEBUG_INT = 10;
    final public int INFO_INT = 20;
    final public int WARN_INT = 30;
    final public int ERROR_INT = 40;

    public void log(Marker marker, String fqcn, int level, String message, Object[] argArray, Throwable t);

}
```

可以知道级别从小到大依次为：

> * TRACE		0
> * DEBUG		10
> * INFO		20
> * WARN		30
> * ERROR		40

# 二 logback的使用

### 两种配置方式

* 1. application.yml（简便）
* 2. logback-spring.xml（功能完善，推荐）

### logback的功能需求

* 1. 区分info和error日志
* 2. 每天产生一个日志文件

当我们在yml中配置时：

![](/uploads/190121java2/2.png)

可以看到输出明显和之前不一样了，说明可以在logging: pattern: console中配置文件的输出格式，这里只配置了**时间 - 信息 换行符**。

还可以在这里配置其他的如日志生成目录，日志级别等。

但是在yml中功能会有很多制约，所以我们一般使用logback-spring.xml的方式。

在**resource**下新建和application.yml同级的文件logback-spring.xml。

logback-spring.xml：

```
<?xml version="1.0" encoding="UTF-8" ?>

<configuration>
    <!--日志格式的配置-->
    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                %d - %msg%n
            </pattern>
        </layout>
    </appender>

    <!--配置info级别的滚动的文件输出，每天生成一篇-->
    <appender name="fileInfoLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--设置过滤输出范围 注意 设置为INFO比INFO高的也会打印出来 所以使用后面那种方法-->
        <!--<filter class="ch.qos.logback.classic.filter.ThresholdFilter">-->
            <!--<level>INFO</level>-->
        <!--</filter>-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>DENY</onMatch>
            <onMismatch>ACCEPT</onMismatch>
        </filter>
        <encoder>
            <pattern>
                %msg%n
            </pattern>
        </encoder>
        <!--滚动策略，按照时间来滚动，每天一个日志文件-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--路径-->
            <fileNamePattern>/Users/binzhang/Documents/log/sell/info.%d.log</fileNamePattern>
        </rollingPolicy>
    </appender>

    <!--配置error级别的滚动的文件输出，每天生成一篇-->
    <appender name="fileErrorLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--设置过滤输出范围-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
        <encoder>
            <pattern>
                %msg%n
            </pattern>
        </encoder>
        <!--滚动策略，按照时间来滚动，每天一个日志文件-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--路径-->
            <fileNamePattern>/Users/binzhang/Documents/log/sell/error.%d.log</fileNamePattern>
        </rollingPolicy>
    </appender>

    <root level="info">
        <appender-ref ref="consoleLog" />
        <appender-ref ref="fileInfoLog" />
        <appender-ref ref="fileErrorLog" />
    </root>

</configuration>
```

配置好后再次执行LoggerTest测试类，发现两条需求均已满足，在目录下生成了对应的error和info日志文件，且里面内容对应正确。

至此，springboot与logback配置成功。



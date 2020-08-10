



---

##### Logback：logback-spring.xml, logback-spring.groovy, logback.xml, logback.groovy

##### Log4j：log4j-spring.properties, log4j-spring.xml, log4j.properties, log4j.xml

##### Log4j2：log4j2-spring.xml, log4j2.xml

##### JDK (Java Util Logging)：logging.properties

##### @Slf4j注解跟[log.info](https://links.jianshu.com/go?to=http%3A%2F%2Flog.info)配合使用，属于lombok工具方式输出，直接输出到控制台，调试的时候特别方便。另外一种[fileLog.info](https://links.jianshu.com/go?to=http%3A%2F%2FfileLog.info)会特定输出到指定的日志文件

示例一

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration >
    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
    <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />
    <!-- 彩色日志格式 -->
    <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%logger){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}" />
    <!--包名输出缩进对齐-->
    <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}" />


    <contextName>fanxlxs</contextName>

    <!--文件夹在当前项目磁盘根目录-->
    <property name="LOG_PATH" value="/modellog" />
    <!--设置系统日志目录-->
    <property name="APPDIR" value="logs" />

    <!--  日志记录器，日期滚动记录
            ERROR 级别
     -->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/${APPDIR}/model_repatile.log</file>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 归档的日志文件的路径，例如今天是1992-11-06日志，当前写的日志文件路径为file节点指定，
            可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
            而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
            <fileNamePattern>${LOG_PATH}/${APPDIR}/error/model_repatile-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
            命名日志文件，例如log-error-1992-11-06.0.log -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!-- 追加方式记录日志 -->
        <append>true</append>
        <!-- 日志文件的格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <!-- 此日志文件记录error级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <!-- 日志记录器，日期滚动记录
            WARN  级别
     -->
    <appender name="WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/${APPDIR}/model_repatile_warn.log</file>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 归档的日志文件的路径，例如今天1992-11-06日志，当前写的日志文件路径为file节点指定，
            可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
            而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
            <fileNamePattern>${LOG_PATH}/${APPDIR}/warn/model_repatile-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
            命名日志文件，例如log-warn-1992-11-06.0.log -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!-- 追加方式记录日志 -->
        <append>true</append>
        <!-- 日志文件的格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <!-- 此日志文件只记录warn级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>warn</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <!-- 日志记录器，日期滚动记录
            INFO  级别
    -->
    <appender name="INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/${APPDIR}/model_repatile_ginfo.log</file>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 归档的日志文件的路径，例如今天是1992-11-06日志，当前写的日志文件路径为file节点指定，
            可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
            而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
            <fileNamePattern>${LOG_PATH}/${APPDIR}/info/model_repatile-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
            命名日志文件，例如log-info-1992-11-06.0.log -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!-- 追加方式记录日志 -->
        <append>true</append>
        <!-- 日志文件的格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <!-- 此日志文件只记录info级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>info</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <!-- 日志记录器，日期滚动记录
            DEBUG  级别
    -->
    <appender name="DEBUG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/${APPDIR}/model_repatile_debug.log</file>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 归档的日志文件的路径，例如今天是1992-11-06日志，当前写的日志文件路径为file节点指定，
            可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
            而1992-11-06的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引 -->
            <fileNamePattern>${LOG_PATH}/${APPDIR}/debug/model_repatile-debug-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!-- 除按日志记录之外，还配置了日志文件不能超过10MB，若超过10MB，日志文件会以索引0开始，
            命名日志文件，例如log-debug-1992-11-06.0.log -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!-- 追加方式记录日志 -->
        <append>true</append>
        <!-- 日志文件的格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%thread] %logger Line:%-3L - %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <!-- 此日志文件只记录debug级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>debug</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- ConsoleAppender 控制台输出日志 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!--encoder 默认配置为PatternLayoutEncoder-->
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <!--<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level -&#45;&#45; [%thread] %logger Line:%-3L - %msg%n</pattern>-->
            <charset>utf-8</charset>
        </encoder>
        <!--此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>debug</level>
        </filter>
    </appender>

    <!-- FrameworkServlet日志-->
    <logger name="org.springframework" level="WARN" />

    <!-- mybatis日志打印-->
    <logger name="org.apache.ibatis" level="DEBUG" />
    <logger name="java.sql" level="DEBUG" />

    <!--  项目 mapper 路径
            console控制台显示sql语句：STDOUT.filter.level -> debug级别
    -->
    <logger name="com.limit.mapper" level="DEBUG"></logger>

    <!-- 生产环境下，将此级别配置为适合的级别，以免日志文件太多或影响程序性能 -->
    <root level="INFO">
        <appender-ref ref="ERROR" />
        <appender-ref ref="WARN" />
        <appender-ref ref="INFO" />
        <appender-ref ref="DEBUG" />
        <!-- 生产环境将请stdout去掉 -->
        <appender-ref ref="STDOUT" />
    </root>

</configuration>
```

示例二

[jmx功能来实现动态修改日志级别](https://blog.csdn.net/devotedwife/article/details/81915332?utm_source=blogxgwz0)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />
    <!-- 开启jmx功能来实现动态修改日志级别 -->
    <jmxConfigurator/>
    ​
    <springProperty scope="context" name="springAppName" source="spring.application.name" />
    <!-- Example for logging into the build folder of your project -->
    <property name="LOG_FILE" value="/data/logs/crm/${springAppName}" />
    ​
    <!-- You can override this to have a custom pattern -->
    <property name="CONSOLE_LOG_PATTERN" value="%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}" />

    <springProfile name="local,dev,test">
        <!-- Appender to log to console -->
        <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
            <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
                <!-- Minimum logging level to be presented in the console logs -->
                <level>DEBUG</level>
            </filter>
            <encoder>
                <pattern>${CONSOLE_LOG_PATTERN}</pattern>
                <charset>utf8</charset>
            </encoder>
        </appender>
        <root level="INFO">
            <appender-ref ref="console" />
        </root>
        <!--打印sql语句-->
        <!-- <logger name="com.mili.boss.alarm.mapper" level="debug" additivity="false">
            <appender-ref ref="console" />
        </logger> -->

    </springProfile>

    <springProfile name="test,pre,prod">
        <!-- Appender to log to file -->
        <appender name="flatfile" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <file>${LOG_FILE}.log</file>
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>${LOG_FILE}.%d{yyyy-MM-dd}.gz</fileNamePattern>
                <maxHistory>30</maxHistory>
            </rollingPolicy>
            <encoder>
                <pattern>${CONSOLE_LOG_PATTERN}</pattern>
                <charset>utf8</charset>
            </encoder>
        </appender>

        <appender name="logstash"
                  class="ch.qos.logback.core.rolling.RollingFileAppender">
            <file>${LOG_FILE}.json</file>
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>${LOG_FILE}.json.%d{yyyy-MM-dd}.gz
                </fileNamePattern>
                <maxHistory>7</maxHistory>
            </rollingPolicy>
            <encoder
                    class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
                <providers>
                    <timestamp>
                        <timeZone>UTC+8</timeZone>
                    </timestamp>
                    <pattern>
                        <pattern>
                            {
                            "severity": "%level",
                            "service": "${springAppName:-}",
                            "trace": "%X{X-B3-TraceId:-}",
                            "span": "%X{X-B3-SpanId:-}",
                            "parent": "%X{X-B3-ParentSpanId:-}",
                            "exportable": "%X{X-Span-Export:-}",
                            "pid": "${PID:-}",
                            "thread": "%thread",
                            "class": "%logger{40}",
                            "rest": "%message"
                            }
                        </pattern>
                    </pattern>
                </providers>
            </encoder>
        </appender>
        <root level="INFO">
            <appender-ref ref="flatfile" />
            <!-- uncomment this to have also JSON logs -->
            <appender-ref ref="logstash"/>
        </root>
    </springProfile>
</configuration>
```

[logback配置异步日志](https://www.cnblogs.com/yangzhilong/p/10577613.html)

```xml
<appender name="FILE" class= "ch.qos.logback.core.rolling.RollingFileAppender">  
            <!-- 按天来回滚，如果需要按小时来回滚，则设置为{yyyy-MM-dd_HH} -->  
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">  
                 <fileNamePattern>/opt/log/test.%d{yyyy-MM-dd}.log</fileNamePattern>  
                 <!-- 如果按天来回滚，则最大保存时间为1天，1天之前的都将被清理掉 -->  
                 <maxHistory>30</maxHistory>  
            <!-- 日志输出格式 -->  
            <layout class="ch.qos.logback.classic.PatternLayout">  
                 <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} -%msg%n</Pattern>  
            </layout>  
</appender>  
     <!-- 异步输出 -->  
     <appender name ="ASYNC" class= "ch.qos.logback.classic.AsyncAppender">  
            <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->  
            <discardingThreshold >0</discardingThreshold>  
            <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->  
            <queueSize>512</queueSize>  
            <!-- 添加附加的appender,最多只能添加一个 -->  
         <appender-ref ref ="FILE"/>  
     </appender>  
       
     <root level ="info">  
            <appender-ref ref ="ASYNC"/>  
     </root>
```

异步配置参数：

| 属性名              | 类型    | 描述                                                         |
| ------------------- | ------- | ------------------------------------------------------------ |
| queueSize           | int     | BlockingQueue的最大容量，默认情况下，大小为256。             |
| discardingThreshold | int     | 默认情况下，当BlockingQueue还有20%容量，他将丢弃TRACE、DEBUG和INFO级别的event，只保留WARN和ERROR级别的event。为了保持所有的events，设置该值为0。 |
| includeCallerData   | boolean | 提取调用者数据的代价是相当昂贵的。为了提升性能，默认情况下，当event被加入到queue时，event关联的调用者数据不会被提取。默认情况下，只有"cheap"的数据，如线程名。 |


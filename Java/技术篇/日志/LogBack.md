[TOC]

#### 一、概述

LogBack 是一个日志框架，它与 Log4j 可以说是同出一源，都出自 Ceki Gülcü 之手。（ Log4j 的原型是早前由Ceki Gülcü 贡献给 Apache 基金会的）下载地址：<http://logback.qos.ch/download.html>

#### 二、LogBack、Slf4j和Log4j之间的关系

Slf4j 是 The Simple Logging Facade for Java 的简称，是一个简单日志门面抽象框架，它本身只提供了日志 Facade API 和一个简单的日志类实现，一般常配合 Log4j，LogBack，java.util.logging 使用。Slf4j 作为应用层的Log 接入时，程序可以根据实际应用场景动态调整底层的日志实现框架(Log4j/LogBack/JdkLog…)。

LogBack 和 Log4j 都是开源日记工具库，LogBack 是 Log4j 的改良版本，比 Log4j 拥有更多的特性，同时也带来很大性能提升。

LogBack 官方建议配合 Slf4j 使用，这样可以灵活地替换底层日志框架。

#### 三、LogBack的结构

LogBack 被分为3个组件，logback-core, logback-classic 和 logback-access。

其中 logback-core 提供了 LogBack 的核心功能，是另外两个组件的基础。

logback-classic 则实现了 Slf4j 的 API，所以当想配合 Slf4j 使用时，需要将 logback-classic 加入 classpath。

logback-access 是为了集成 Servlet 环境而准备的，可提供 HTTP-access 的日志接口。

#### 四、配置详解

##### 1. 根节点`<configuration>`包含的属性

- scan：当此属性设置为 true 时，配置文件如果发生改变，将会被重新加载，默认值为 true；
- scanPeriod：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当 scan 为 true 时，此属性生效。默认的时间间隔为1分钟；
- debug：当此属性设置为 true 时，将打印出 LogBack 内部日志信息，实时查看 LogBack 运行状态。默认值为 false。

XML代码为：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!-- Logback configuration. See http://logback.qos.ch/manual/index.html -->
<configuration scan="true" scanPeriod="10 seconds" debug="false">
    <!-- 其他配置省略-->
</configuration>
```

##### 2. 根节点`<configuration>`的子节点

LogBack 的配置大概包括3部分：appender、logger 和 root。

###### 设置上下文名称`<contextName>`

每个 LogBack 都关联到 Logger 上下文，默认上下文名称为“default”。但可以使用`<contextName>`设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!-- Logback configuration. See http://logback.qos.ch/manual/index.html -->
<configuration scan="true" scanPeriod="10 seconds" debug="false">
    <contextName>myAppName</contextName>
    <!-- 其他配置省略-->
</configuration>
```

###### 设置变量`<property>`

用来定义变量值的标签，`<property>`有两个属性，name 和 value；其中 name 的值是变量的名称，value 的值时变量定义的值。通过`<property>`定义的值会被插入到 Logger 上下文中。定义变量后，可以使“${}”来使用变量。

例如使用`<property>`定义上下文名称，然后在`<contentName>`设置 Logger 上下文时使用：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!-- Logback configuration. See http://logback.qos.ch/manual/index.html -->
<configuration scan="true" scanPeriod="10 seconds" debug="false">
    <property name="APP_Name" value="myAppName" />
    <contextName>${APP_Name}</contextName>
    <!-- 其他配置省略-->
</configuration>
```

> PS：想使用 spring 扩展 profile 支持，要以 logback-spring.xml 命名，其他如 property 需要改为 springProperty。

###### 获取时间戳字符串`<timestamp>`

两个属性

1. key：标识此`<timestamp>`的名字；

2. datePattern：设置将当前时间（解析配置文件的时间）转换为字符串的模式，遵循`Java.txt.SimpleDateFormat`的格式。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!-- Logback configuration. See http://logback.qos.ch/manual/index.html -->
<configuration scan="true" scanPeriod="10 seconds" debug="false">
    <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>
    <contextName>${bySecond}</contextName>
    <!-- 其他配置省略-->
</configuration>
```

###### `Logger`

用来设置某一个包或者具体的某一个类的日志打印级别、以及指定`<appender>`。`<logger>`仅有一个`name`属性，一个可选的`level`和一个可选的`additivity`属性。

- name：用来指定受此 Logger 约束的某一个包或者具体的某一个类。
- level：用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，还有一个特殊值 INHERITED 或者同义词 NULL，代表强制执行上级的级别。如果未设置此属性，那么当前 Logger 将会继承上级的级别。
- additivity：是否向上级 Logger传递打印信息。默认是 true。

`<logger>`可以包含零个或多个`<appender-ref>`元素，标识这个 appender 将会添加到这个 Logger。

###### `<root>`

也是`<logger>`元素，但是它是根 Logger。只有一个`level`属性，应为已经被命名为”root”。

- level：用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为 INHERITED 或者同义词 NULL。默认是 DEBUG。

`<root>`可以包含零个或多个`<appender-ref>`元素，标识这个 appender 将会添加到这个 Logger。

##### 3. `<appender>`详解

`<appender>`是`<configuration>`的子节点，是负责写日志的组件。`<appender>`有两个必要属性`name`和`class`。`name`指定 appender 名称，`class`指定 appender 的全限定名。

###### ch.qos.logback.core.ConsoleAppender

把日志添加到控制台，有以下子节点：

- `<encoder>`：对日志进行格式化。
- `<target>`：字符串 System.out 或者 System.err ，默认 System.out

###### ch.qos.logback.core.FileAppender

把日志添加到文件，有以下子节点：

- `<file>`：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
- `<append>`：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是 true。
- `<encoder>`：对记录事件进行格式化。
- `<prudent>`：如果是 true，日志会被安全的写入文件，即使其他的Fil eAppender 也在向此文件做写入操作，效率低，默认是 false。

###### ch.qos.logback.core.rolling.RollingFileAppender

滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件。有以下子节点：

- `<file>`：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
- `<append>`：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是 true。
- `<encoder>`：对记录事件进行格式化。
- `<rollingPolicy>`：当发生滚动时，决定 RollingFileAppender 的行为，涉及文件移动和重命名。
  - TimeBasedRollingPolicy：最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责触发滚动。有以下子节点：
    - `<fileNamePattern>`：必要节点，包含文件名及“%d”转换符，%d”可以包含一个`Java.text.SimpleDateFormat`指定的时间格式，如：`%d{yyyy-MM}`。如果直接使用 %d，默认格式是`yyyy-MM-dd`。RollingFileAppender 的 file 字节点可有可无，通过设置file，可以为活动文件和归档文件指定不同位置，当前日志总是记录到 file 指定的文件（活动文件），活动文件的名字不会改变；如果没设置 file，活动文件的名字会根据 fileNamePattern 的值，每隔一段时间改变一次。“/”或者“\”会被当做目录分隔符。
    - `<maxHistory>`：可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每个月滚动，且`<maxHistory>`是6，则只保存最近6个月的文件，删除之前的旧文件。注意，删除旧文件是，那些为了归档而创建的目录也会被删除。
  - FixedWindowRollingPolicy：根据固定窗口算法重命名文件的滚动策略。有以下子节点：
    - `<minIndex>`：窗口索引最小值。
    - `<maxIndex>`：窗口索引最大值，当用户指定的窗口过大时，会自动将窗口设置为12。
    - `<fileNamePattern>`：必须包含“%i”例如，假设最小值和最大值分别为1和2，命名模式为 mylog%i.log,会产生归档文件mylog1.log和mylog2.log。还可以指定文件压缩选项，例如，mylog%i.log.gz 或者 没有log%i.log.zip
  - SizeBasedTriggeringPolicy：查看当前活动文件的大小，如果超过指定大小会告知RollingFileAppender 触发当前活动文件滚动。只有一个节点:
    - `<maxFileSize>`：这是活动文件的大小，默认值是 10MB。
- `<triggeringPolicy >`：告知 RollingFileAppender 何时激活滚动。
- `<prudent>`：当为 true 时，不支持 FixedWindowRollingPolicy。支持 TimeBasedRollingPolicy，但是有两个限制，1不支持也不允许文件压缩，2不能设置file属性，必须留空。

另外还有`SocketAppender`、`SMTPAppender`、`DBAppender`、`SyslogAppender`、`SiftingAppender`，并不常用。

##### 4. `<encoder>`

负责两件事，一是把日志信息转换成字节数组，二是把字节数组写入到输出流。

目前`PatternLayoutEncoder`是唯一有用的且默认的 encoder ，有一个`<pattern>`节点，用来设置日志的输入格式。使用“%”加“转换符”方式，如果要输出“%”，则必须用“\”对“\%”进行转义。

#### 五、整合spring配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!-- Logback configuration. See http://logback.qos.ch/manual/index.html -->
<configuration scan="true" scanPeriod="10 seconds" debug="false">
    <!--继承spring boot提供的logback配置-->
    <!--<include resource="org/springframework/boot/logging/logback/base.xml" />-->

    <!--设置系统日志目录-->
    <property name="APP_DIR" value="D:\Java\Workspace\IDEA\MyProjects\Dubbo\my-blog\app-sso" />

    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
    <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />
    <!-- 彩色日志格式 -->
    <property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan}[lineno:%line] %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>

    <!-- 控制台输出 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
            <!-- 此处设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
        <!--临界值过滤器，此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 DEBUG 日志 -->
    <appender name="DEBUG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/log_debug.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <!-- 此处设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
                归档的日志文件的路径，例如今天是2017-04-26日志，当前写的日志文件路径为file节点指定，可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
                而2017-04-26的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引
            -->
            <fileNamePattern>${LOG_PATH}/debug/log-debug-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!--
                除按日志记录之外，还配置了日志文件不能超过500M，若超过500M，日志文件会以索引0开始，
                命名日志文件，例如log-error-2017-04-26.0.log
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>500MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <!-- 级别过滤器，此日志文件只记录debug级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>DEBUG</level>
            <!-- 用于配置符合过滤条件的操作 -->
            <onMatch>ACCEPT</onMatch>
            <!-- 用于配置不符合过滤条件的操作 -->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 INFO 日志 -->
    <appender name="INFO_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/log_info.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <!-- 此处设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
                归档的日志文件的路径，例如今天是2017-04-26日志，当前写的日志文件路径为file节点指定，可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
                而2017-04-26的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引
            -->
            <fileNamePattern>${LOG_PATH}/info/log-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!--
                除按日志记录之外，还配置了日志文件不能超过500M，若超过500M，日志文件会以索引0开始，
                命名日志文件，例如log-error-2017-04-26.0.log
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>500MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录info级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 WARN 日志 -->
    <appender name="WARN_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/log_warn.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <!-- 此处设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
                归档的日志文件的路径，例如今天是2017-04-26日志，当前写的日志文件路径为file节点指定，可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
                而2017-04-26的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引
            -->
            <fileNamePattern>${LOG_PATH}/warn/log-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!--
                除按日志记录之外，还配置了日志文件不能超过500M，若超过500M，日志文件会以索引0开始，
                命名日志文件，例如log-error-2017-04-26.0.log
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>500MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录warn级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 ERROR 日志 -->
    <appender name="ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${LOG_PATH}/log_error.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <!-- 此处设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
                归档的日志文件的路径，例如今天是2017-04-26日志，当前写的日志文件路径为file节点指定，可以将此文件与file指定文件路径设置为不同路径，从而将当前日志文件或归档日志文件置不同的目录。
                而2017-04-26的日志文件在由fileNamePattern指定。%d{yyyy-MM-dd}指定日期格式，%i指定索引
            -->
            <fileNamePattern>${LOG_PATH}/error/log-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!--
                除按日志记录之外，还配置了日志文件不能超过500M，若超过500M，日志文件会以索引0开始，
                命名日志文件，例如log-error-2017-04-26.0.log
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>500MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录ERROR级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <logger name="org.springframework.web" level="INFO"/>
    <logger name="org.springframework.scheduling.annotation.ScheduledAnnotationBeanPostProcessor" level="INFO"/>
    <logger name="com.cn.app.sso" level="debug"/>

    <!--开发环境:打印控制台-->
    <springProfile name="dev">
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="DEBUG_FILE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="WARN_FILE" />
            <appender-ref ref="ERROR_FILE" />
        </root>
    </springProfile>

    <!--测试环境:打印控制台和输出到文件-->
    <springProfile name="test">
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="WARN_FILE" />
            <appender-ref ref="ERROR_FILE" />
        </root>
    </springProfile>

    <!--生产环境:输出到文件-->
    <springProfile name="prod">
        <root level="INFO">
            <appender-ref ref="DEBUG_FILE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="ERROR_FILE" />
        </root>
    </springProfile>
</configuration>
```


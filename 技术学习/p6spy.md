# 							p6sp

# 准备

## 依赖

```
        <!--p6spy-->
        <dependency>
            <groupId>p6spy</groupId>
            <artifactId>p6spy</artifactId>
            <version>3.7.0</version>
        </dependency>
```

## 配置类

```
public class P6SpyLogger implements MessageFormattingStrategy {

    @Override
    public String formatMessage(int connectionId, String now, long elapsed, String category, String prepared, String sql) {
        return !"".equals(sql.trim()) ? "[ " + LocalDateTime.now() + " ] --- | took "
                + elapsed + "ms | " + category + " | connection " + connectionId + "\n "
                + sql + ";" : "";
    }
}
```

## 配置文件

spy.properties

```
module.log=com.p6spy.engine.logging.P6LogFactory,com.p6spy.engine.outage.P6OutageFactory
# 自定义日志打印
logMessageFormat=com.ruigu.skoyi.store.config.P6SpyLogger
# 使用日志系统记录sql
appender=com.p6spy.engine.spy.appender.Slf4JLogger
## 配置记录Log例外
excludecategories=info,debug,result,batc,resultset
# 设置使用p6spy driver来做代理
deregisterdrivers=true
# 日期格式
dateformat=yyyy-MM-dd HH:mm:ss
# 实际驱动 
driverlist=com.mysql.cj.jdbc.Driver
# 是否开启慢SQL记录
outagedetection=true
# 慢SQL记录标准 秒
outagedetectioninterval=2
```

## url配置

```
    url: jdbc:p6spy:mysql://localhost:3306/master0?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
    username: root
    password: mysql
    driver-class-name: com.p6spy.engine.spy.P6SpyDriver
```



# P6spy是什么？

P6Spy 是针对数据库访问操作的动态监测框架（为开源项目，项目首页：www.p6spy.com）它使得数据库数据可无缝截取和操纵，而不必对现有应用程序的代码作任何修改。P6Spy 分发包包括P6Log，它是一个可记录任何 Java 应用程序的所有JDBC事务的应用程序。其配置完成使用时，可以进行数据访问性能的监测。

我们最需要的功能，查看sql语句，不是预编译的带问号的哦，而是真正的数据库执行的sql，更直观，更简单。














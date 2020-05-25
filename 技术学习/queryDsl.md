# 一 QueryDsl


# 依赖

```
        <!--querydsl-->
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-jpa</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-apt</artifactId>
        </dependency>
 

 
<!--该插件可以生成querysdl需要的查询对象，执行mvn compile即可-->
           <plugin>
                <groupId>com.mysema.maven</groupId>
                <artifactId>apt-maven-plugin</artifactId>
                <version>1.1.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>process</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>target/generated-sources/java</outputDirectory>
                            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                            <options>
                                <querydsl.entityAccessors>true</querydsl.entityAccessors>
                                <querydsl.createDefaultVariable>true</querydsl.createDefaultVariable>
                                <querydsl.packageSuffix>.qdsl</querydsl.packageSuffix>
                            </options>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
```

# 配置

```
@Configuration
public class JpaAutoConfiguration {
    @Bean({"JpaFactory"})
    public JPAQueryFactory jpaQueryFactory(EntityManager entityManager) {
        return new JPAQueryFactory(entityManager);
    }
} 
```

# 常用方法

## 时间

```
 LocalDateTime startTime = localDate.atStartOfDay();
 LocalDateTime endTime = localDate.atTime(LocalTime.MAX);
 LocalDateTime endTime = LocalDateTime.of(req.getEndDate(), LocalTime.MAX)
 
 当月第一天
 LocalDateTime startTime = LocalDate.of(month.getYear(), month.getMonthValue(), 1).atStartOfDay();
```



## 返回自定义 实体类

```
 https://cloud.tencent.com/developer/article/1152904
 public ServerResponse<PageImpl<BaseRuiguSupplierVO>> queryRuiGuSupplers(QuerySupplierListReq req) {
        QBaseRuiguSupplierEntity supplierEntity = QBaseRuiguSupplierEntity.baseRuiguSupplierEntity;
        // 拼接查询条件
        BooleanBuilder condition = new BooleanBuilder();
        if (req.getSupplierType() != null) {
            condition.and(supplierEntity.supplierType.eq(req.getSupplierType()));
        }
        if (StringUtils.isNotEmpty(req.getKeyWord())) {
            condition.and(supplierEntity.phone.like("%" + req.getKeyWord() + "%"));
            condition.or(supplierEntity.supplierName.like("%" + req.getKeyWord() + "%"));
            condition.or(supplierEntity.contact.like("%" + req.getKeyWord() + "%"));
        }
        condition.and(supplierEntity.deleted.eq(YesOrNoEnum.NO.getCode()));
        Pageable pageable = PageRequest.of(req.getPage(), req.getSize());
        JPAQuery<BaseRuiguSupplierVO> allSupplierEntity = jpaQueryFactory.select(Projections.bean(BaseRuiguSupplierVO.class,
                supplierEntity.id,
                supplierEntity.phone,
                supplierEntity.supplierName,
                supplierEntity.supplierType,
                supplierEntity.contact))
                .from(supplierEntity).where(condition)
                .orderBy(supplierEntity.lastUpdatedAt.desc())
                .fetchAll();
        List<BaseRuiguSupplierVO> voList = allSupplierEntity.offset(pageable.getOffset()).limit(pageable.getPageSize()).fetch();
        return ServerResponse.ok(PageImpl.of(voList, pageable, allSupplierEntity.fetchCount()));
    }
```

## 全部方法

```
all method:
1.  adjustInto  调整指定的Temporal和当前LocalDateTime对
2.  atOffset    结合LocalDateTime和ZoneOffset创建一个
3.  atZone  结合LocalDateTime和指定时区创建一个ZonedD
4.  compareTo   比较两个LocalDateTime
5.  format  格式化LocalDateTime生成一个字符串
6.  from    转换TemporalAccessor为LocalDateTi
7.  get 得到LocalDateTime的指定字段的值
8.  getDayOfMonth   得到LocalDateTime是月的第几天
9.  getDayOfWeek    得到LocalDateTime是星期几
10. getDayOfYear    得到LocalDateTime是年的第几天
11. getHour 得到LocalDateTime的小时
12. getLong 得到LocalDateTime指定字段的值
13. getMinute   得到LocalDateTime的分钟
14. getMonth    得到LocalDateTime的月份
15. getMonthValue   得到LocalDateTime的月份，从1到12
16. getNano 得到LocalDateTime的纳秒数
17. getSecond   得到LocalDateTime的秒数
18. getYear 得到LocalDateTime的年份
19. isAfter 判断LocalDateTime是否在指定LocalDateT
20. isBefore    判断LocalDateTime是否在指定LocalDateT
21. isEqual 判断两个LocalDateTime是否相等
22. isSupported 判断LocalDateTime是否支持指定时间字段或单元
23. minus   返回LocalDateTime减去指定数量的时间得到的值
24. minusDays   返回LocalDateTime减去指定天数得到的值
25. minusHours  返回LocalDateTime减去指定小时数得到的值
26. minusMinutes    返回LocalDateTime减去指定分钟数得到的值
27. minusMonths 返回LocalDateTime减去指定月数得到的值
28. minusNanos  返回LocalDateTime减去指定纳秒数得到的值
29. minusSeconds    返回LocalDateTime减去指定秒数得到的值
30. minusWeeks  返回LocalDateTime减去指定星期数得到的值
31. minusYears  返回LocalDateTime减去指定年数得到的值
32. now 返回指定时钟的当前LocalDateTime
33. of  根据年、月、日、时、分、秒、纳秒等创建LocalDateTi
34. ofEpochSecond   根据秒数(从1970-01-0100:00:00开始)创建L
35. ofInstant   根据Instant和ZoneId创建LocalDateTim
36. parse   解析字符串得到LocalDateTime
37. plus    返回LocalDateTime加上指定数量的时间得到的值
38. plusDays    返回LocalDateTime加上指定天数得到的值
39. plusHours   返回LocalDateTime加上指定小时数得到的值
40. plusMinutes 返回LocalDateTime加上指定分钟数得到的值
41. plusMonths  返回LocalDateTime加上指定月数得到的值
42. plusNanos   返回LocalDateTime加上指定纳秒数得到的值
43. plusSeconds 返回LocalDateTime加上指定秒数得到的值
44. plusWeeks   返回LocalDateTime加上指定星期数得到的值
45. plusYears   返回LocalDateTime加上指定年数得到的值
46. query   查询LocalDateTime
47. range   返回指定时间字段的范围
48. toLocalDate 返回LocalDateTime的LocalDate部分
49. toLocalTime 返回LocalDateTime的LocalTime部分
50. toString    返回LocalDateTime的字符串表示
51. truncatedTo 返回LocalDateTime截取到指定时间单位的拷贝
52. until   计算LocalDateTime和另一个LocalDateTi
53. with    返回LocalDateTime指定字段更改为新值后的拷贝
54. withDayOfMonth  返回LocalDateTime月的第几天更改为新值后的拷贝
55. withDayOfYear   返回LocalDateTime年的第几天更改为新值后的拷贝
56. withHour    返回LocalDateTime的小时数更改为新值后的拷贝
57. withMinute  返回LocalDateTime的分钟数更改为新值后的拷贝
58. withMonth   返回LocalDateTime的月份更改为新值后的拷贝
59. withNano    返回LocalDateTime的纳秒数更改为新值后的拷贝
60. withSecond  返回LocalDateTime的秒数更改为新值后的拷贝
61. withYear    返回LocalDateTime年份更改为新值后的拷贝

```














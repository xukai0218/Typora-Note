

# 							Sharding - JDBC

# 依赖

```
       <!--sharding 配置-->
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.0.0</version>
       </dependency>
-------------------------------------------------------------------------------------       
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.0.0</version>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.shardingsphere</groupId>
                    <artifactId>sharding-core-route</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.ruigu.rbox.shardingsphere</groupId>
            <artifactId>sharding-core-route</artifactId>
            <version>4.0.0</version>
        </dependency>
```



# 一、什么是Sharding-JDBC

Sharding-JDBC定位为轻量级Java框架，在Java的JDBC层提供的额外服务。它使用客户端直连数据库，以jar包形式提供服务，无需额外部署和依赖，可理解为增强版的JDBC驱动，完全兼容JDBC和各种ORM框架。

# 二、Sharding-JDBC能做什么

- 分库 & 分表

- 读写分离

- 分布式主键

- 分布式事务

  

# 三、适用项目框架

Sharding-JDBC适用于：

- 任何基于Java的ORM框架，如：JPA, Hibernate, Mybatis, Spring JDBC Template或直接使用JDBC。
- 基于任何第三方的数据库连接池，如：DBCP, C3P0, BoneCP, Druid, HikariCP等。
- 支持任意实现JDBC规范的数据库，目前支持MySQL，Oracle，SQLServer和PostgreSQL。





# 1.分库分区分表概念

分区
    就是把一张表的数据分成N个区块，在逻辑上看最终只是一张表，但底层是由N个物理区块组成的

分表
    就是把一张数据量很大的表按一定的规则分解成N个具有独立存储空间的实体表。系统读写时需要根据定义好的规则得到对应的字表明，然后操作它。表名可以按照某种业务hash进行映射。

分库
    一旦分表，一个库中的表会越来越多



## **1.为什么要分表：**

当一张表的数据达到几千万时，你查询一次所花的时间会变多，如果有联合查询的话，我想有可能会死在那儿了。分表的目的就在于此，减小数据库的负担，缩短查询时间。





```
Caused by: io.shardingsphere.core.exception.ShardingException: Cannot get uniformed table structure for `t`. The different meta data of actual tables are as follows

配置的表是t0,t1，但是数据库实际只创建了t0表，所以抛出上述异常，再创建t1表即可解决问题
```

```
支持inline表达式
member_index_tbl_${[a,b,c]}会被解析成
member_index_tbl_${0..2}会解析成member_index_tbl_0，member_index_tbl_1,member_index_tbl_2；
member_index_tbl_a,member_index_tbl_b和member_index_tbl_c,
```

```
erver.port=9001

# 数据库连接池配置变量
initialSize=5
minIdle=5
maxIdle=100
maxActive=20
maxWait=60000
timeBetweenEvictionRunsMillis=60000
minEvictableIdleTimeMillis=300000

####################################
# configuration of DataSource
####################################
sharding.jdbc.datasource.names=ds0,ds1

sharding.jdbc.datasource.ds0.type=com.alibaba.druid.pool.DruidDataSource
sharding.jdbc.datasource.ds0.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds0.url=jdbc:mysql://localhost:3306/demo_ds_0
sharding.jdbc.datasource.ds0.username=root
sharding.jdbc.datasource.ds0.password=123456
# 初始连接数
sharding.jdbc.datasource.ds0.initialSize=${initialSize}
# 最小连接数
sharding.jdbc.datasource.ds0.minIdle=${minIdle}
# 最大连接池数量
sharding.jdbc.datasource.ds0.maxActive=${maxActive}
# 配置获取连接等待超时的时间
sharding.jdbc.datasource.ds0.maxWait=${maxWait}
# 用来检测连接是否有效的sql
sharding.jdbc.datasource.ds0.validationQuery=SELECT 1 FROM DUAL
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
sharding.jdbc.datasource.ds0.timeBetweenEvictionRunsMillis=${timeBetweenEvictionRunsMillis}
# 配置一个连接在池中最小生存的时间，单位是毫秒
sharding.jdbc.datasource.ds0.minEvictableIdleTimeMillis=${minEvictableIdleTimeMillis}

sharding.jdbc.datasource.ds1.type=com.alibaba.druid.pool.DruidDataSource
sharding.jdbc.datasource.ds1.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds1.url=jdbc:mysql://localhost:3306/demo_ds_1
sharding.jdbc.datasource.ds1.username=root
sharding.jdbc.datasource.ds1.password=123456
# 初始连接数
sharding.jdbc.datasource.ds1.initialSize=${initialSize}
# 最小连接数
sharding.jdbc.datasource.ds1.minIdle=${minIdle}
# 最大连接池数量
sharding.jdbc.datasource.ds1.maxActive=${maxActive}
# 配置获取连接等待超时的时间
sharding.jdbc.datasource.ds1.maxWait=${maxWait}
# 用来检测连接是否有效的sql
sharding.jdbc.datasource.ds1.validationQuery=SELECT 1 FROM DUAL
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
sharding.jdbc.datasource.ds1.timeBetweenEvictionRunsMillis=${timeBetweenEvictionRunsMillis}
# 配置一个连接在池中最小生存的时间，单位是毫秒
sharding.jdbc.datasource.ds1.minEvictableIdleTimeMillis=${minEvictableIdleTimeMillis}

####################################
# 分库分表配置
####################################
#actual-data-nodes：真实数据节点，由数据源名 + 表名组成，以小数点分隔。多个表以逗号分隔，支持inline表达式
sharding.jdbc.config.sharding.tables.t_user.actual-data-nodes=ds${0..1}.t_user_${0..1}
# 数据库分片列名称
sharding.jdbc.config.sharding.default-database-strategy.inline.sharding-column=user_id
# 分库算法表达式(取模 , HASH , 分块等)
sharding.jdbc.config.sharding.default-database-strategy.inline.algorithm-expression=ds${user_id % 2}

# 分表字段
sharding.jdbc.config.sharding.tables.t_user.table-strategy.inline.sharding-column=user_id
# 分表策略，这里不能跟分库策略一样，否则会导致有一半数据表没有机会插入数据
sharding.jdbc.config.sharding.tables.t_user.table-strategy.inline.algorithm-expression=t_user_${(user_id % 5) % 2}
# 配置自动生成主键
sharding.jdbc.config.sharding.tables.t_user.key-generator-column-name=user_id


# 配置生成自增ID的雪花算法，单台服务器可以不配置
spring.shardingsphere.sharding.tables.t_user.key-generator.column=user_id
spring.shardingsphere.sharding.tables.t_user.key-generator.type=SNOWFLAKE
spring.shardingsphere.sharding.tables.t_user.key-generator.props.worker.id=1
spring.shardingsphere.sharding.tables.t_user.key-generator.props.max.tolerate.time.difference.milliseconds=0
spring.shardingsphere.props.sql.show=true

# open debug mode for mybatis，print SQL in console
logging.level.org.rockyang.shardingjdbc.common.mapper=DEBUG
logging.level.org.springframework=INFO
mybatis.configuration.cache-enabled=false
```

# 配置

## 分库配置

```
sharding:
  jdbc:
    datasource:
      names: ds_0,ds_1
      ds_0:
           type: com.zaxxer.hikari.HikariDataSource
           driver-class-name: com.mysql.jdbc.Driver
           jdbc-url: jdbc:mysql://sharding0
           username: mams_test
           password: mams_test
      ds_1:
           type: com.zaxxer.hikari.HikariDataSource
           driver-class-name: com.mysql.jdbc.Driver
           jdbc-url: jdbc:mysql://sharding1
           username: mams_test
           password: mams_test
    config:
      sharding:
      	broadcast-tables: pm_meta_category # 配置广播表，适合数据量不大，但是每个数据源都存在的表
        default-data-source-name: ds_0
        tables:
            task:
                actual-data-nodes: ds_${0..1}.task
                database-strategy:
                    inline:
                        sharding-column: id
                        algorithm-expression: ds_${id % 2}
                key-generator-column-name: id
            reverse_display:
                actual-data-nodes: ds_${0..1}.reverse_display
                database-strategy:
                    inline:
                        sharding-column: task_id
                        algorithm-expression: ds_${task_id % 2}
                key-generator-column-name: id
```



## 读写分离

```

spring:
  shardingsphere:
    datasource:
      names: db0,db1
      db0:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/db0?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8&allowPublicKeyRetrieval=true
        username: root
        password: 123456
      db1:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/db1?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8&allowPublicKeyRetrieval=true
        username: root
        password: 123456
    sharding:
      default-database-strategy:
        inline:
          sharding-column: id
          algorithm-expression: db$->{id%2}
      tables:
        user:
          actual-data-nodes: db0.user_${0..1}
          table-strategy:
            inline:
              sharding-column: id
              algorithm-expression: user_$->{id%2}
    masterslave:
      name: master-name
      master-data-source-name: db0
      slave-data-source-names: db1
```

## 读写分离参考01

```
######################### sharding_jdbc读写分离配置 ###################################
#######################################################################################
### 说明：
###  1、读取数据使用从数据库(slave),多个从数据库算法：round_robin（轮询）和random（随机）
###  2、写入数据使用主数据库(master，添加，修改，删除)
###  3、读写分离配置 master为主，slave为从,多个从数据库逗号分隔
###  4、sharding.jdbc 没有做主从复制,需要自己处理多个数据库之间的同步数据操作
###  5、ds0.type=com.alibaba.druid.pool.DruidDataSource 代表的当前使用数据源(我这里是 Druid)


spring:
  application:
    name: sharding-jdbc
  #  datasource:
  #    url: jdbc:p6spy:mysql://localhost:3306/master0?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
  #    username: root
  #    password: mysql
  #    driver-class-name: com.p6spy.engine.spy.P6SpyDriver
  shardingsphere:
    datasource:
      names: master0,master0slave0,master0slave1
      master0:
        jdbcUrl: jdbc:p6spy:mysql://localhost:3306/master0?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
        username: root
        password: mysql
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: com.p6spy.engine.spy.P6SpyDriver
      master0slave0:
        jdbcUrl: jdbc:p6spy:mysql://localhost:3306/master0slave0?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
        username: root
        password: mysql
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: com.p6spy.engine.spy.P6SpyDriver
      master0slave1:
        jdbcUrl: jdbc:p6spy:mysql://localhost:3306/master0slave1?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
        username: root
        password: mysql
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: com.p6spy.engine.spy.P6SpyDriver
    masterslave:
      name: ms
#        round_robin（轮询）和random（随机）。
      load-balance-algorithm-type: round_robin
      master-data-source-name:  master0
      slave-data-source-names: master0slave0,master0slave1
    props:
      sql:
        show: true

```

02

```
    sharding:
      master-slave-rules:
        ds-0:
          master-data-source-name: ds-0
          slave-data-source-names: ds-0-slave
        ds-1:
          master-data-source-name: ds-1
          slave-data-source-names: ds-1-slave
```



## 分库+读写分离

```

```



# 坑

```
  datasource:
      names: ds_0,ds_1 -->  ds-0,ds-1

Description:

Configuration property name 'sharding.jdbc.datasource.ds_0' is not valid:

    Invalid characters: '_'
    Bean: servletEndpointRegistrar
    Reason: Canonical names should be kebab-case ('-' separated), lowercase alpha-numeric characters and must start with a letter

Action:

Modify 'sharding.jdbc.datasource.ds_0' so that it conforms to the canonical names requirements.
```

```
: query did not return a unique result: 2; nested exception is javax.persistence.NonUniqueResultException: query did not return a unique result: 2
```



覆盖

```
The bean 'dataSource', defined in class path resource [io/shardingsphere/shardingjdbc/spring/boot/SpringBootConfiguration.class], could not be registered. A bean with that name has already been defined in class path resource [com/alibaba/druid/spring/boot/autoconfigure/DruidDataSourceAutoConfigure.class] and overriding is disabled.


spring:
  main:
    allow-bean-definition-overriding: true
```



```
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'io.shardingsphere.shardingjdbc.spring.boot.SpringBootConfiguration': Initialization of bean failed; nested exception is java.lang.NullPointerException

type: com.alibaba.druid.pool.DruidDataSource
```



4.0 配置

```
shardingsphere:
    datasource:
      ds0:
        driver-class-name: com.mysql.jdbc.Driver
        password: chusen
        type: com.zaxxer.hikari.HikariDataSource
        jdbcUrl: jdbc:mysql://localhost:3306/product0?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
        username: root
      ds1:
        driver-class-name: com.mysql.jdbc.Driver
        password: chusen
        type: com.zaxxer.hikari.HikariDataSource
        jdbcUrl: jdbc:mysql://localhost:3306/product1?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
        username: root
      names: ds0,ds1

    sharding:
      default-database-strategy:
#        inline:
#          sharding-column: id
#          algorithm-expression: ds$->{id % 2}
        standard:
          precise-algorithm-class-name: it.chusen.algorithm.PreciseModuloDataBaseShardingAlgorithm
          sharding-column: id

      tables:
        pm_product:
          actual-data-nodes: ds$->{0..1}.pm_product$->{0..1}
          key-generator:
            column: id
            type: snowflake
          database-strategy:
            standard:
              precise-algorithm-class-name: com.ruigu.bpos.customer.config.DefinitionDataSourceAlgorithm
             sharding-column: mobile  
          table-strategy:
            inline:
              algorithm-expression: pm_product$->{id % 2}
              sharding-column: id
        pm_log:
          actual-data-nodes: ds$->{0..1}.pm_log
          key-generator:
            column: id
            type: snowflake
    props:
      sql:
        show: true
```

# 分库后的问题

## 1雪花算法id  精度问题

```
问题: 后端把Long类型的数据传给前端，前端可能会出现精度丢失的情况。例如：201511200001725439这样一个Long类型的整数，传给前端后会变成201511200001725440
解决 1: vo类   private String id;	  
	 
    2:	vo类  @JsonFormat(shape = JsonFormat.Shape.STRING)
    		  private Long id;	  	  
```

## 2 更新分库键问题

```
org.apache.shardingsphere.core.exception.ShardingException: Can not update sharding key, logic table: [customer_info], column: [org.apache.shardingsphere.sql.parser.sql.segment.dml.assignment.AssignmentSegment@36380c9].
修改 pom shardingsphere 依赖
修改源码  排除原本依赖  添加修改后依赖  ShardingUpdateStatementValidator
        <!--sharding 配置-->
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.0.0</version>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.shardingsphere</groupId>
                    <artifactId>sharding-core-route</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.ruigu.rbox.shardingsphere</groupId>
            <artifactId>sharding-core-route</artifactId>
            <version>4.0.0</version>
        </dependency>
```



## 3 sql 注解

```
    @Query(" select p.salePrice from ProductEntity p where p.id=:productId and p.shopId = :shopId and p.deleted = :deleted")
    BigDecimal findFirstByIdAndShopIdAndDeleted(@Param("productId") Long productId, @Param("shopId") Long shopId, @Param("deleted") Integer deleted);
```

4 查找多个结果

```
org.hibernate.HibernateException: More than one row with the given identifier was found: 1657, for class: 
            PmMetaCategoryEntity metaCategoryEntity = metaCategoryRepository.findById(req.getMetaCategoryId()).orElseThrow(() -> new LogicException(req.getMetaCategoryId() + " : 该二级分类不存在！"));
            
                    pm_meta_category:
          actual-data-nodes: ds-0.pm_meta_category
          
                  pm_meta_category:
          key-generator:
            column: id
            type: snowflake


```

## 模式错误

```
Caused by: java.sql.SQLSyntaxErrorException: Expression #4 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'bpos_sa_0.saproducti0_.sale_unit' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
select @@global.sql_mode;
select @@sql_mode;

set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';

https://www.cnblogs.com/kenshinobiy/p/9580701.html
```



## 添加分片字段

```
    需要注意的是，分表后，我们原来的SQL都要Check一遍，看是否带上了分片字段，否则会进行全表扫描，性能比较差。
```

```
org.springframework.dao.InvalidDataAccessResourceUsageException: Named parameter not bound : __$synthetic$__8; nested exception is org.hibernate.QueryException: Named parameter not bound : __$synthetic$__8


    @Query("select new com.ruigu.bpos.product.model.vo.ProductForOrderVO " +
            "(p.id, p.prodName, p.shopId, p.model, p.salePrice, " +
            "p.image, pmc.id,pmc.catePath, b.brandName,b.id, p.purchasePrice) " +
            "from ProductEntity p " +
            "inner join " +
            "ProductCategoryAssociationEntity pca on p.id = pca.prodId " +
            "inner join " +
            "PmMetaCategoryEntity as pmc on pmc.id = pca.categoryId " +
            "left join " +
            "PmBrandEntity b on b.id = p.brandId " +
            "where p.shopId = :#{#req.getShopId()} " +
            "and p.enabled = 1 " +
            "and p.deleted = 0 " +
            "and (:#{#req.getBrandId()} is null or p.brandId = :#{#req.getBrandId()}) " +
            "and (:#{#req.getCategoryId()} is null or pmc.id = :#{#req.getCategoryId()}) " +
            "and (:#{#req.getPmName()} is null or p.prodName like CONCAT('%',:#{#req.getPmName()},'%'))" +
            "and (:#{#req.isExistProdIds()} is null or p.id in :#{#req.getProdIds()}) ")
    Page<ProductForOrderVO> findForOrder(@Param("req") QueryProductForOrderReq req, Pageable pageable);
```

## 字段source

```
line 1:155 no viable alternative at input '(content,created_at,created_by,last_updated_at,new_value,old_value,opera_id,opera_type,order_id,prop_type,remark,source'


line 1:511 no viable alternative at input 'ordersaleh0_.source'
```


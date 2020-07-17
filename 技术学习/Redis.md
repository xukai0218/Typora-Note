

# 依赖

```
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

```
    @Autowired
    private StringRedisTemplate redisTemplate;
```

```
spring: 
  redis:
    port: 6379
    database: 0
    host: 192.168.2.10
```



# 常用命令

Zremrangebyrank 命令用于移除有序集中，指定排名(rank)区间内的所有成员。

```
ZREMRANGEBYRANK key start stop
```

Zinterstore

```
 命令计算给定的一个或多个有序集的交集，其中给定 key 的数量必须以 numkeys 参数指定，并将该交集(结果集)储存到 destination 。

默认情况下，结果集中某个成员的分数值是所有给定集下该成员分数值之和。

ZINTERSTORE 新集合 num(几个集合求交) collect1 collect2 collect3
ZINTERSTORE newCollect 3 collect1 collect2 collect3
```



模糊查key  keys rbox:search:brand:*

	get rbox:search:category:1000

zset			
zrange rbox:search:matrix:category:1694 0 -1;

输出带分数的结果为
zrange rbox:search:matrix:category:1694 0 -1 withscores;

zrange test-promotion:promotion:city_code:310110:promotionIds 0 -1 withscores

zrange test-promotion:promotion:order_type:4:promotionIds 0 -1 withscores


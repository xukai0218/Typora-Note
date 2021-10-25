

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





scan  批量清理缓存

```
package com.ruigu.obm.base.manager;

import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.Cursor;
import org.springframework.data.redis.core.RedisConnectionUtils;
import org.springframework.data.redis.core.ScanOptions;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;

/**
 * @author xukai
 * @date 2021/8/22 10:49 上午
 */
@Component
public class RedisManager {

    @Autowired
    private RedisConnectionFactory redisConnectionFactory;

    /**
     * redis 模糊删除
     *
     * @param key
     */
    public void deleteKeys(String key) {
        if (StringUtils.isBlank(key)) {
            return;
        }
        RedisConnection connection = null;
        try {
            connection = redisConnectionFactory.getConnection();
            List<byte[]> keyList = keys(key);

            byte[][] keysArray = keyList.toArray(new byte[0][]);
            if (keysArray.length > 0) {
                connection.del(keysArray);
            }
        } finally {
            RedisConnectionUtils.releaseConnection(connection, redisConnectionFactory, false);
        }

    }


    /**
     * scan 实现
     *
     * @param pattern  表达式
     * @param consumer 对迭代到的key进行操作
     */
    private void scan(String pattern, Consumer<byte[]> consumer) {
        RedisConnection connection = redisConnectionFactory.getConnection();
        try (Cursor<byte[]> cursor = connection.scan(ScanOptions.scanOptions().count(Long.MAX_VALUE).match(pattern).build())) {
            cursor.forEachRemaining(consumer);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }

    /**
     * 获取符合条件的key
     *
     * @param pattern 表达式
     * @return
     */
    public List<byte[]> keys(String pattern) {
        List<byte[]> keys = new ArrayList<>();
        this.scan(pattern, keys::add);
        return keys;
    }
}

// 清除redis 缓存 key : obm:base:brand*   
 //key = key + "*";
   //     log.info("清除redis 缓存 key : {} ", key);
     //   redisManager.deleteKeys(key);
```


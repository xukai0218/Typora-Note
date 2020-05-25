---
typora-root-url: ..\image
---

# SpringBoot Cache

## 1.依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

##  2.开启缓存

```java
@SpringBootApplication
@EnableCaching 
public class DemoApplication{

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

## 3.缓存`@Cacheable`

`@Cacheable`注解会先查询是否已经有缓存，有会使用缓存，没有则会执行方法并缓存。

此处的value是必需的，它指定了你的缓存存放在哪块命名空间。

```
@Cacheable(value = {Constants.REDIS_SA_CACHE_KEY}, key = "methodName + ':storeId:' +#p0 +':startDate:' +#p1.getStartDate() +':endDate:'+ #p1.getEndDate()")
```

```
String REDIS_SA_CACHE_KEY = "skoyi:store:sa";
```

```
@Cacheable(value = {Constants.REDIS_SA_CACHE_KEY}, key = "'storeId:' +#p0")
public HomeDashboardVO queryHomeBoard(Integer storeId) {
    return null;
}
```

## 4.清除缓存@CacheEvict

### 清除单个条件

```
@CacheEvict(cacheNames = {Constants.REDIS_SA_CACHE_KEY},key = "'storeId:' +#p0+':*'")
public String createOrder(Integer storeId, OrderReq req) {
	return null;
}
```

### 多个查询条件

```
@Caching(evict = {
@CacheEvict(cacheNames = {Constants.REDIS_SA_CACHE_KEY}, key = "'storeId:' + #storeId + '*'"),
@CacheEvict(cacheNames = {Constants.REDIS_PRODUCT_CACHE_KEY}, key = "'storeId:' + #storeId + '*'", condition = "#req.status.equals(2)")
    })
```



## 5.设置缓存与清除缓存配置

### RedisConfig.java

```
/**
 * @author xukai
 * @create 2020-03-26 22:18
 */
@Configuration
@EnableCaching
public class RedisConfig implements Serializable {

    /**
     * 设置redis缓存过期时间
     */
    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
        return new RedisCacheManager(
                RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory),
                // 默认策略，未配置的 key 会使用这个
                this.getRedisCacheConfigurationWithTtl(-1),
                this.getRedisCacheConfigurationMap()
        );
    }

    /**
     * 自定义指定 key 设置过期时间
     */
    private Map<String, RedisCacheConfiguration> getRedisCacheConfigurationMap() {
        Map<String, RedisCacheConfiguration> redisCacheConfigurationMap = new HashMap<>();
        redisCacheConfigurationMap.put(Constants.REDIS_SA_CACHE_KEY, this.getRedisCacheConfigurationWithTtl(Constants.REDIS_CACHE_EXPIRE_TIME));
      
        return redisCacheConfigurationMap;
    }

    private RedisCacheConfiguration getRedisCacheConfigurationWithTtl(Integer seconds) {
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();
        redisCacheConfiguration = redisCacheConfiguration.serializeValuesWith(
                RedisSerializationContext
                        .SerializationPair
                        .fromSerializer(jackson2JsonRedisSerializer)
        ).entryTtl(Duration.ofSeconds(seconds));

        return redisCacheConfiguration;
    }

    @Bean("redisTemplate")
    public RedisTemplate<String, Object> getRedisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(connectionFactory);
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);

        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);

        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }
}

```





### CustomizedRedisCache.java

```
import org.springframework.core.convert.ConversionService;
import org.springframework.data.redis.cache.RedisCache;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheWriter;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.Cursor;
import org.springframework.data.redis.core.ScanOptions;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;

public class CustomizedRedisCache extends RedisCache {

    private final ConversionService conversionService;
    private final RedisConnectionFactory redisConnectionFactory;

    protected CustomizedRedisCache(String name, RedisCacheWriter cacheWriter, RedisCacheConfiguration cacheConfig, RedisConnectionFactory redisConnectionFactory) {
        super(name, cacheWriter, cacheConfig);
        this.conversionService = cacheConfig.getConversionService();
        this.redisConnectionFactory = redisConnectionFactory;
    }


    /**
     * 解决 @CacheEvict 模糊删除问题
     *
     * @param key
     */
    @Override
    public void evict(Object key) {
        if (key instanceof String && key.toString().contains("*")) {
            String pattern = this.conversionService.convert(this.createCacheKey(key), String.class);
            RedisConnection connection = redisConnectionFactory.getConnection();
            List<byte[]> keyList = keys(pattern);

            byte[][] keysArray = keyList.toArray(new byte[0][]);
            if (keysArray.length > 0) {
                connection.del(keysArray);
            }
        }
        super.evict(key);
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
    private List<byte[]> keys(String pattern) {
        List<byte[]> keys = new ArrayList<>();
        this.scan(pattern, keys::add);
        return keys;
    }
}
```



###  CustomizedRedisCacheManager

```
import org.springframework.data.redis.cache.RedisCache;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.cache.RedisCacheWriter;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.lang.Nullable;

import java.util.Collections;
import java.util.HashMap;
import java.util.Map;


public class CustomizedRedisCacheManager extends RedisCacheManager {

    private final RedisCacheWriter cacheWriter;
    private final RedisCacheConfiguration defaultCacheConfig;
    private RedisConnectionFactory redisConnectionFactory;


    public CustomizedRedisCacheManager(RedisCacheWriter cacheWriter, RedisCacheConfiguration defaultCacheConfiguration, Map<String, RedisCacheConfiguration> initialCacheConfigurations) {
        super(cacheWriter, defaultCacheConfiguration, initialCacheConfigurations);
        this.cacheWriter = cacheWriter;
        this.defaultCacheConfig = defaultCacheConfiguration;
    }

    public CustomizedRedisCacheManager(RedisConnectionFactory redisConnectionFactory, RedisCacheConfiguration cacheConfiguration, Map<String, RedisCacheConfiguration> initialCacheConfigurations) {
        this(RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory), cacheConfiguration, initialCacheConfigurations);
        this.redisConnectionFactory = redisConnectionFactory;
    }

    @Override
    protected RedisCache createRedisCache(String name, @Nullable RedisCacheConfiguration cacheConfig) {
        return new CustomizedRedisCache(name, cacheWriter, cacheConfig != null ? cacheConfig : defaultCacheConfig, redisConnectionFactory);
    }

    @Override
    public Map<String, RedisCacheConfiguration> getCacheConfigurations() {
        Map<String, RedisCacheConfiguration> configurationMap = new HashMap<>(getCacheNames().size());
        getCacheNames().forEach(it -> {
            RedisCache cache = CustomizedRedisCache.class.cast(lookupCache(it));
            configurationMap.put(it, cache != null ? cache.getCacheConfiguration() : null);
        });
        return Collections.unmodifiableMap(configurationMap);
    }
}
```
















































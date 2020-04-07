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

```
@CacheEvict(cacheNames = {Constants.REDIS_SA_CACHE_KEY},key = "'storeId:' +#p0+':*'")
public String createOrder(Integer storeId, OrderReq req) {
	return null;
}
```



## 5.缓存设置过期时间

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



## 


















































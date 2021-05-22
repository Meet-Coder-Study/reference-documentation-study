# 7week

https://www.notion.so/Reference-Documentation-Study-1-d385c8e2705844e2b28181fcd71cb59d

## Graceful shutdown
https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-graceful-shutdown

```yaml
server:
  shutdown: "graceful"
``` 


## Caching

https://beanbroker.github.io/2019/10/05/Kotlin/spring_redis_client_kotlin/

> lettuce is a scalable thread-safe Redis client based on netty and Reactor. Lettuce provides synchronous, asynchronous and reactive APIs to interact with Redis.

- spring boot 2.xx then lettuce
- spring boot 1.xx then jedis


```bash
docker run -p 6379:6379 --name pkj-redis -d redis

docker exec -i -t pkj-redis redis-cli

keys *
```

- @EnableCaching 어노테이션 필수!
- 캐싱사용법 3가지
    - @EnableRedisRepository 로 사용
    - 직접 redisTemplate 주입하여 활용
    - @Cacheable 활용



> redis config 예시

```java
@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport {

  @Value("${spring.redis.host}")
  private String redisHost;

  @Value("${spring.redis.port}")
  private int redisPort;

  @Bean
  public RedisConnectionFactory redisConnectionFactory() {

    RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration();
    redisStandaloneConfiguration.setHostName(redisHost);
    redisStandaloneConfiguration.setPort(redisPort);

    LettuceConnectionFactory lettuceConnectionFactory =
        new LettuceConnectionFactory(redisStandaloneConfiguration);
    return lettuceConnectionFactory;
  }

  @Bean(name = "redisJsonTemplate")
  public RedisTemplate<String, ?> redisJsonTemplate() {

    RedisSerializer<String> serializer = new StringRedisSerializer();
    GenericJackson2JsonRedisSerializer genericJackson2JsonRedisSerializer =
        new GenericJackson2JsonRedisSerializer();

    RedisTemplate<String, ?> template = new RedisTemplate<>();
    template.setKeySerializer(serializer);
    template.setValueSerializer(genericJackson2JsonRedisSerializer);
    template.setHashKeySerializer(serializer);
    template.setHashValueSerializer(genericJackson2JsonRedisSerializer);

    template.setConnectionFactory(redisConnectionFactory());
    template.setEnableTransactionSupport(true);

    return template;
  }

  @Bean(name = RedisName.PKJ_ONE_MINUTE_CACHE)
  public CacheManager oneMinuteCache() {

    RedisSerializationContext.SerializationPair<Object> jsonSerializer =
        RedisSerializationContext.SerializationPair.fromSerializer(
            new GenericJackson2JsonRedisSerializer());

    return RedisCacheManager.RedisCacheManagerBuilder.fromConnectionFactory(
            redisConnectionFactory())
        .cacheDefaults(
            RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(1))
                .serializeValuesWith(jsonSerializer)
                .disableCachingNullValues())
        .build();
  }

  @Override
  public CacheErrorHandler errorHandler() {
    return new RedisCacheErrorHandler();
  }

  @Slf4j
  private static class RedisCacheErrorHandler extends SimpleCacheErrorHandler {

    @Override
    public void handleCacheGetError(RuntimeException exception, Cache cache, Object key) {

      log.error("error to get data from cache : ", exception);
    }
  }
}
```

> @Cacheable 어노테이션을 활용해보자

```java
@Cacheable(
      value = RedisValue.NAME_CACHE,
      cacheManager = RedisName.PKJ_ONE_MINUTE_CACHE,
      unless = "#result==null",
      key = "'user-name' + #id")
  public String getUserName(String userId) {

    log.info("call redis");

    return "pkj".equals(userId) ? "pkj" : null;
  }
```

왜 GenericJackson2JsonRedisSerializer를 써야할가?

## Messing

- https://beanbroker.github.io/2018/11/10/Spring/messageQ/ 로 대체

## RestTemplate vs WebClient

blocking vs Non-Blocking

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html

```
Please, consider using the org.springframework.web.reactive.client.WebClient which has a more modern API and supports sync, async, and streaming scenarios.
```





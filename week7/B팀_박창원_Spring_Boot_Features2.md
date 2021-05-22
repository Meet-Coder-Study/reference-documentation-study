# Caching

스프링 프레임워크는 애플리케이션에 캐싱 기능을 추가할 수 있도록 지원한다.

스프링은 캐싱 기능을 추상화하여 메소드에 캐싱을 적용할 수 있도록 한다.

캐싱 로직은 투명하게 적용된다.

캐싱 기능을 활성화하려면 `@EnableCaching` 어노테이션을 붙여줘야 한다.



## 버퍼와 캐시의 차이점

버퍼(buffer)와 캐시(cache)라는 두 용어는 혼용되어 사용되기도 한다. 하지만, 엄연히 다른 의미를 갖는다.

버퍼는 옛부터 빠르고 느린 두 개체 사이의 임시 저장소를 가리킨다. 

버퍼는 두 개체 사이의 속도 차이를 완화하는 역할을 한다.

버퍼는 데이터를 읽고 쓰는 것을 한번만 한다.

캐시는 숨겨져 있는 존재다. 데이터를 요구하는 개체는 캐싱이 발생하는 것조차 알지 못한다.

버퍼와 다르게, 같은 데이터를 여러번 읽을 수도 있다.

캐시 추상화는 자바 메서드에 캐싱을 하는 것을 말한다.

지원하는 캐시 프로바이더

`org.springframework.cache.Cache` 와 `org.springframework.cache.CacheManager` 인터페이스로 표현되는 캐시 추상화는 특정한 저장소나 구현을 제공하지 않는다.

만약, `CacheManager` 나 `CacheResolver` 빈을 구현하지 않으면, 스프링 부트는 다음의 프로바이더를 감지하려고 시도할 것이다.

1. [Generic](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-generic)
2. [JCache (JSR-107)](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-jcache) (EhCache 3, Hazelcast, Infinispan, and others)
3. [EhCache 2.x](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-ehcache2)
4. [Hazelcast](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-hazelcast)
5. [Infinispan](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-infinispan)
6. [Couchbase](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-couchbase)
7. [Redis](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-redis)
8. [Caffeine](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-caffeine)
9. [Simple](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-simple)

만약 `CacheManager` 가 스프링 부트에 의해 자동 설정된다면, 아래와 같이 CacheManagerCustomizer 인터페이스를 구현하는 빈을 생성하면 된다.

```java
@Bean
public CacheManagerCustomizer<ConcurrentMapCacheManager> cacheManagerCustomizer() {
    return new CacheManagerCustomizer<ConcurrentMapCacheManager>() {

        @Override
        public void customize(ConcurrentMapCacheManager cacheManager) {
            cacheManager.setAllowNullValues(false);
        }

    };
}
```



내부적으로, 여러분의 서비스에 캐싱 기능을 적용하려면 다음과 같이 작성한다.

```java
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Component;

@Component
public class MathService {

    @Cacheable("piDecimals")
    public int computePiDecimal(int i) {
        // ...
    }

}
```

위 코드는 `computePiDecimal` 이 컴퓨팅 비용이 높은 작업이라고 가정한다.

`computePiDecimal` 메소드를 실제로 실행하기 전에, `piDecimlas` 캐시에 인자 `i` 와 일치하는 것이 있는지 확인하고 만약 있다면, 메소드를 호출한 caller에게 곧바로 캐싱된 값이 반환된다.

그렇지 않다면, 메서드의 본문은 실행되고 캐시가 업데이트 되고나서 값이 반환된다.







## JCache (JSR-107)

[JCache](https://jcp.org/en/jsr/detail?id=107)를 부트스트랩하려면, `javax.cache.spi.CachingProvider` 가 클래스패스 상에 존재해야 한다. 또한, `JCacheCacheManager` 가 스프링 부트에 의해 자동제공된다.

(jcache는 2019 이후로 업데이트가 없다.)

```properties
# Only necessary if more than one provider is present
spring.cache.jcache.provider=com.acme.MyCachingProvider
spring.cache.jcache.config=classpath:acme.xml
```

## EhCache 2.x

[EhCache](https://www.ehcache.org/) 2.x은 `ehcache.xml` 파일이 클래스패스의 루트 상에 존재하면 감지된다. 

EhCache 2.x가 존재한다면, 스프링 부트는 `spring-boot-starter-cache` 는 `EhCacheCacheManager` 를 제공한다.

먼저 resources 폴더에 ehcache.xml파일을 생성한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <diskStore path="java.io.tmpdir" />

    <cache name="findRandomNumber"
           maxEntriesLocalHeap="10000"
           maxEntriesLocalDisk="1000"
           eternal="false"
           diskSpoolBufferSizeMB="20"
           timeToIdleSeconds="300" timeToLiveSeconds="600"
           memoryStoreEvictionPolicy="LFU"
           transactionalMode="off">
        <persistence strategy="localTempSwap" />
    </cache>

</ehcache>
```



```java

@RestController
public class CacheTester implements ApplicationRunner {

	private static final Logger logger = LoggerFactory.getLogger(CacheTester.class);

	private final CacheManager cacheManager;
	private final CacheTarget cacheTarget;


	@Autowired
	public CacheTester(CacheManager cacheManager, CacheTarget cacheTarget) {
		this.cacheManager = cacheManager;
		this.cacheTarget = cacheTarget;
	}

	@Override
	public void run(ApplicationArguments args) throws Exception {
		logger.info(cacheManager.getClass().getName());
	}

	@GetMapping("/cache")
	public int get() {
		return cacheTarget.compute();
	}
}

@EnableCaching
@Configuration
class CacheConfiguration {
}
@Component
class CacheTarget {
	private static final Logger logger = LoggerFactory.getLogger(CacheTarget.class);

	Random random = new Random(10);

	@Cacheable(value = "findRandomNumber")
	public int compute() {
		int result = random.nextInt();
		logger.info("target computed, {}", result);
		return result;
	}
}


```



cache의 로그 레벨을 TRACE로 올려서 확인해보면 다음과 같이 찍힌다.

xml에서 정의해둔 findRandNumber와 캐시를 적용할 메서드인 CacheTarget.compute()가 서로 연결되었다는 의미다.

```
2021-05-16 15:52:45.546 TRACE 5842 --- [main] o.s.c.a.AnnotationCacheOperationSource   : Adding cacheable method 'compute' with attribute: [Builder[public int me.cwpark.cachespring.CacheTarget.compute()] caches=[findRandomNumber] | key='' | keyGenerator='' | cacheManager='' | cacheResolver='' | condition='' | unless='' | sync='false']
```

```
2021-05-16 16:08:23.741  INFO 5944 --- [           main] me.cwpark.cachespring.CacheTester        : cache class: org.springframework.cache.ehcache.EhCacheCacheManager
```

또한, ApplicationRunner로 구현한 CacheTester.run() 메서드에서 EhCacheCacheManager가 구현되었음을 확인할 수 있다.



터미널을 이용해 여러번 요청해보자

```bash
$ curl -X GET http://localhost:8080/cache
```

로그를 확인하면 CacheTarget의 compute 메서드는 단 한번 호출되었음을 알 수 있다.

```bash
2021-05-16 15:48:42.160  INFO 5768 --- [nio-8080-exec-1] me.cwpark.cachespring.CacheTarget        : target computed, -1157793070
```



앞서 빈 자동주입한 CacheManager가 EhCacheManger의 빈이 되는 과정을 보려면 `EhCacheCacheConfiguration` 에 디버그를 찍어보면 확인할 수 있다.

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({Cache.class, EhCacheCacheManager.class})
@ConditionalOnMissingBean({CacheManager.class})
@Conditional({CacheCondition.class, EhCacheCacheConfiguration.ConfigAvailableCondition.class})
class EhCacheCacheConfiguration {
    EhCacheCacheConfiguration() {
    }

    @Bean
    EhCacheCacheManager cacheManager(CacheManagerCustomizers customizers, net.sf.ehcache.CacheManager ehCacheCacheManager) {
        return (EhCacheCacheManager)customizers.customize(new EhCacheCacheManager(ehCacheCacheManager));
    }

    @Bean
    @ConditionalOnMissingBean
    net.sf.ehcache.CacheManager ehCacheCacheManager(CacheProperties cacheProperties) {
        Resource location = cacheProperties.resolveConfigLocation(cacheProperties.getEhcache().getConfig());
        return location != null ? EhCacheManagerUtils.buildCacheManager(location) : EhCacheManagerUtils.buildCacheManager();
    }

    static class ConfigAvailableCondition extends ResourceCondition {
        ConfigAvailableCondition() {
            super("EhCache", "spring.cache.ehcache.config", new String[]{"classpath:/ehcache.xml"});
        }
    }
}

```



---

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching

https://spring.io/guides/gs/caching/

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-caching-provider-generic

https://jojoldu.tistory.com/57

https://mkyong.com/spring/spring-caching-and-ehcache-example/


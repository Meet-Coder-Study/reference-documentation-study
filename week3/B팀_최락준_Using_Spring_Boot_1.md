# WEEK 3. Using Spring Boot -1

## Auto Configuration

스프링 부트의 auto-configuration은 **추가한 dependency에 따라 자동으로** 설정을 수행한다.  
예를 들어 dependency에 h2를 추가하면, 스프링 부트는 자동으로 메모리 db설정을  수행한다.

auto-configuration을 활성화 하기 위해서는  
**@EnableAutoConfiguration** 또는 **@SpringBootApplication** 어노테이션이 필요하다.


## gradually replacing auto-configuration

자동설정은 언제든지 개별설정으로 대체할 수 있다.  
configuration Bean을 등록하면 **자동설정은 이 bean 설정으로 대체**된다.

`--debug` 옵션을 통해 현재 설정된 auto-configuration을 확인할 수 있다.

다음과 같이 로그에 현재 설정된 목록을 확인할 수 있다.
```
java -jar test.jar --debug
```

```
...
   JacksonAutoConfiguration.ParameterNamesModuleConfiguration matched:
      - @ConditionalOnClass found required class 'com.fasterxml.jackson.module.paramnames.ParameterNamesModule' (OnClassCondition)

   JacksonAutoConfiguration.ParameterNamesModuleConfiguration#parameterNamesModule matched:
      - @ConditionalOnMissingBean (types: com.fasterxml.jackson.module.paramnames.ParameterNamesModule; SearchStrategy: all) did not find any beans (OnBeanCondition)

   JacksonHttpMessageConvertersConfiguration.MappingJackson2HttpMessageConverterConfiguration matched:
      - @ConditionalOnClass found required class 'com.fasterxml.jackson.databind.ObjectMapper' (OnClassCondition)
      - @ConditionalOnProperty (spring.mvc.converters.preferred-json-mapper=jackson) matched (OnPropertyCondition)
      - @ConditionalOnBean (types: com.fasterxml.jackson.databind.ObjectMapper; SearchStrategy: all) found bean 'jacksonObjectMapper' (OnBeanCondition)

...
```

## disabling specific auto-configuration Class

다음과 같이 exclude를 사용하여 원치 않는 설정 클래스를 제외할 수 있다.
```java
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
public class MyApplication {
}
```

다음과 같이 설정 파일을 통해 제외할 수도 있다.

```java
spring.autoconfigure.exclude= \
  org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration, \
  org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration
```

이를 응용하여 테스트 클래스에서도 특정 설정을 제외할 수 있따.
```java
@SpringBootTest(classes = Application.class, webEnvironment = WebEnvironment.DEFINED_PORT)
//테스트에서는 security관련 설정을 사용하지 않음
@EnableAutoConfiguration(exclude=SecurityAutoConfiguration.class)
public class ExcludeAutoConfigIntegrationTest {

    @Test
    public void givenSecurityConfigExcluded_whenAccessHome_thenNoAuthenticationRequired() {
        int statusCode = RestAssured.get("http://localhost:8080/").statusCode();
        assertEquals(HttpStatus.OK.value(), statusCode);
    }
}
```

하지만 실제로 테스트에서는 이 방법이 더 많이 쓰인다.
```java
@SpringBootTest(classes = Application.class, webEnvironment = WebEnvironment.DEFINED_PORT)
// 'test' profile을 사용한다.
@ActiveProfiles("test")
public class ExcludeAutoConfigIntegrationTest {
    // ...
}
```

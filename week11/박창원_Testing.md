# Testing

스프링 부트는 애플리케이션을 테스팅하는 것을 돕는 다양한 유틸리티와 어노테이션을 제공한다.

테스트는 두 개의 모듈에서 제공한다.

- `spring-boot-test` : 코어 아이템을 가지고 있음
- `spring-boot-test-autoconfigure` 테스트를 위한 자동 설정을 지원한다.

## 테스트 스코프 의존성

- [JUnit 5](https://junit.org/junit5/): 자바 애플리케이션을 단위 테스트하기 위한 사실상의 표준(de-facto) 라이브러리
- [Spring Test](https://docs.spring.io/spring/docs/5.3.4/reference/html/testing.html#integration-testing) & Spring Boot Test: 유틸리티와 통합 테스트를 도와줌
- [AssertJ](https://assertj.github.io/doc/): 단언문을 위한 라이브러리
- [Hamcrest](https://github.com/hamcrest/JavaHamcrest): 매쳐 객체 라이브러리
- [Mockito](https://site.mockito.org/): 모킹 프레임워크
- [JSONassert](https://github.com/skyscreamer/JSONassert): JSON 단언문을 위한 라이브러리
- [JsonPath](https://github.com/jayway/JsonPath): JSON XPath



## 스프링 애플리케이션 테스트하기

의존성 주입의 가장 큰 장점 중 하나는 유닛 테스트 하기 용이한 코드를 만든다는 것이다.

Spring을 개입되지 않고도 `new` 연산자를 사용하여 객체를 초기화할 수 있다.

또는, 목 객체를 사용하여 실제 의존 객체를 대체할 수 있다.

스프링 부트 애플리케이션은 `ApplicationContext` 다.



스프링 부트는 `@SpringBootTest` 어노테이션을 제공한다.

이 어노테이션은 스프링 애플리케이션 테스트에서 `ApplicationContext` 를 생성한다.

> @SpringBootTest 어노테이션은 테스트 기반의 스프링 부트를 실행하는 테스트 클래스에 사용된다.
>
> 다음의 기본 Spring TestContext Framework를 제공한다.
>
> - ContextLoader SpringBootContextLoader
>
> - [@ContextConfiguration(loader=...)`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/test/context/ContextConfiguration.html?is-external=true#loader--) 가 지정되지 않았다면, 기본 [ContextLoader](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/test/context/ContextLoader.html?is-external=true) 를 SpringBootContextLoader로 사용한다. 
> - `@Configuration`가 없다면  [`@SpringBootConfiguration`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringBootConfiguration.html) 를 자동으로 탐색한다.
> -  [properties attribute](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#properties--)에 정의된 커스텀 [`Environment`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/core/env/Environment.html?is-external=true)에 정의된 커스텀 [`Environment`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/core/env/Environment.html?is-external=true)에 정의된 커스텀 [`Environment`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/core/env/Environment.html?is-external=true)에 정의된 커스텀 [`Environment`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/core/env/Environment.html?is-external=true) 프로퍼티를 사용한다.
> -  [args attribute](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#args--)를 사용하여 애플리케이션 인자(arguments)를 사용한다.
> - 지정된 포트나 랜덤 포트를 사용하여 웹 서버를 구동하는 것 등의 기능이 포함된 [`webEnvironment`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#webEnvironment--)모드를 사용한다.
> - [`TestRestTemplate`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/web/client/TestRestTemplate.html) 또는, [`WebTestClient`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/test/web/reactive/server/WebTestClient.html?is-external=true) 빈을 자동으로 등록하여 웹 서버를 테스트를 할 수 있다.

## 사용자 설정 및 슬라이싱

만약 여러분의 코드가 이상적인 방식으로 구조화되었다면, `@SpringBootApplication` 클래스가 테스팅 시에 기본 설정 그대로 사용될 수 있다.

애플리케이션의 메인 클래스를 특정 기능이에 국한된 설정 세팅으로 해놓지 않는 것이 중요하다.

예를 들어, Spring Batch를 사용한다면, 다음과 같이 사용할 수 있다.

```java
@SpringBootApplication
@EnableBatchProcessing
public class SampleApplication { ... }
```

이 클래스는 테스트의 소스 설정이므로, 어떠한 슬라이스 테스트든지 Spring Batch를 구동하게 된다. **이것은 분명 여러분이 원하는 결과가 아닐 것이다.**

추천하는 방식은 @Configuration 클래스 마다 특정 기능에 국한된 설정으로 두는 것이다.

```java
@Configuration(proxyBeanMethods = false)
@EnableBatchProcessing
public class BatchConfiguration { ... }
```



## Test Configuration 감지하기

만약 여러분이 Spring Test Framework에 익숙하다면, @ContextConfiguration(classes=..)를 사용하여 어떤 `@Configuration` 을 로드할지 지정하면 된다.

또는, 테스트 내부에 중첩된 @Configuration 클래스를 사용할 수도 있다.

만약 기본 설정(primary configuration)을 커스터마이징하려면, `@Testconfiguration` 클래스를 사용하면 된다.

@Configuration 과 @TestConfiguration의 차이점은, @TestConfiguration은 기본 설정위에 추가적으로 사용되는 클래스이며, @Configuration은 기본 설정을 대체하는 방식으로 적용된다.

> Q) 테스트 케이스마다 **애플리케이션 컨텍스트(application context)**는 초기화되는가?
>
> A) 스프링의 테스트 프레임워크는 애플리케이션 컨텍스트를 캐싱하여 테스트마다 사용한다. 
>
> 따라서, 테스트는 서로 같은 설정을 공유한다.

## Test Configuration 제외하기

만약 애플리케이션이 컴포넌트 스캔을 사용한다면, 특정 테스트에만 사용하길 의도한 최상위의 configuration 클래스가 매번 적용될 수 있다.

최상위 클래스에 `@TestConfiguration` 이 `src/test/java` 에 위치하여도 스캐닝 대상이 되지 안흔ㄴ다.

따라서, 다음과 같이 명시적으로 클래스를 임포트(import)해야 한다.

```java
@SpringBootTest
@Import(MyTestsConfiguration.class)
class MyTests {

    @Test
    void exampleTest() {
        ...
    }

}
```



## TestConfiguration 적용 방법 2가지

### 1. @Import 어노테이션

스프링 테스트 컨텍스트에 적용할 Configuration 클래스를 하나 이상 지정할 수 있다.

@TestConfiguration 클래스에서 정의한 @Bean 은 테스트 클래스에서 @Autowired로 주입받을 수 있다. 또는, configuration 인스턴스 자체가 빈이 되므로 이 빈을 주입할 수 있다.

> XML 기반의 빈 설정을 한다면, @ImportResource 어노테이션을 사용하면 된다.

```java
public class MyTestConfiguration {
 
    //tests specific beans
    @Bean
    DataSource createDataSource(){
        //
    }
}
```



### 2. 정적 중첩 클래스 (Static nested classes)

테스트 클래스 내부에 중첩 클래스로 Test Configuration을 정의하는 방법도 있다.

중첩 클래스는 `@TestConfiguration` 과 `@Configuration`  둘 다 사용할 수 있다.

만약 @TestConfiguration 또는 @Configuration이 정적 중첩 클래스라면, **자동으로 빈 등록이 된다.**

- @Configuration은 기본 설정을 **대체하여** 적용된다.
- 반면, @TestConfiguration은 기본 설정에 **추가로** 적용된다.

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class SpringBootDemoApplicationTests 
{   
    @LocalServerPort
    int randomServerPort;
 
    @Autowired
    DataSource datasource;
 
    //tests
 
    @TestConfiguration
    static class MyTestConfiguration {
 
        //tests specific beans
        @Bean
        DataSource createDataSource(){
            //
        }
    }
}
```



---

https://howtodoinjava.com/spring-boot2/testing/springboot-test-configuration/

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html

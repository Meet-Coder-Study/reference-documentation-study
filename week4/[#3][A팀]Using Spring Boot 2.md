# 5. Spring Beans and Dependency Injection

- 빈들을 정의하고 의존성을 주입하기 위해 스프링 프레임워크의 여러 방법을 자유롭게 사용할 수 있다.
- 주로 빈을 찾을 때 @ComponentScan, 생성자 주입에 @Autowired 를 주로 사용한다.

```
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

- 만약 코드가 위와 같은 형태로, [Application.java](http://application.java) 클래스가 루트 패키지에 존재하는 경우에는 @ComponentScan 을 아무런 아규먼트 없이 붙일 수 있다.
- 어플리케이션 내 모든 컴포넌트들이 자동으로 스프링 빈으로 주입될 것이다.

    (@Component, @Service, @Repository, @Controller 등)

```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    @Autowired
    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...

}
```

- RiskAssessor 를 생성자에서 주입받아 사용하는 서비스 예시
- 빈이(여기서는 DatabaseAccountService) 단 하나의 생성자만 가진 경우 @Autowired를 생략할 수 있다.
- 빈으로 주입하는 RiskAssessor 필드는 final 키워드를 붙여 불변 객체로 만드는 것이 좋다.

# 6. Using the @SpringBootApplication Annotation

### @SpringBootApplication

- `@EnableAutoConfiguration`: 스프링 부트 자동 설정을 가능케한다.
- `@ComponentScan`: 애플리케이션이 위치한 패키지를 포함한 하위 패키지에서 컴포넌트 빈들을 스캔할 수 있도록 한다
- `@Configuration`: 컨텍스트에 추가적인 빈들을 등록할 수 있게 하거나 추가적인 configuration 클래스를 임포트 할 수 있게 한다.

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication 
// same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

### @AliasFor

@AliasFor 을 이용해 메타 어노테이션의 attribute 를 커스터마이징 할 수 있다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class), @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

    @AliasFor(annotation = EnableAutoConfiguration.class)
    Class<?>[] exclude() default {};

    @AliasFor(annotation = EnableAutoConfiguration.class)
    String[] excludeName() default {};
 
    @AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
    String[] scanBasePackages() default {};
 
    @AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
    Class<?>[] scanBasePackageClasses() default {};
 
    @AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;
 
    @AliasFor(annotation = Configuration.class)
    boolean proxyBeanMethods() default true;

}
```

- @SpringBootApplication 은 메타 어노테이션으로 `@SpringBootConfiguration`, `@EnableAutoConfiguration`, `@ComponentScan` 을 사용한다
- 위의 메타 어노테이션을 모두 붙일바엔 `@SpringBootApplication` 하나 붙이는게 편리.
- 해당 메타 어노테이션들이 사용하는 어트리뷰트 값을 커스터마이징하기 위해 `@AliasFor` 을 사용한다

예를 들면 @SpringBootApplication(scanBasePackages = ... ) 과 같이 어노테이션을 붙이면, @ComponentScan 어노테이션의 basePackages 값으로 ... 이 사용된다.

### 필요한 어노테이션만 사용해도 된다.

```java
@Configuration(proxyBeanMethods = false)
@EnableAutoConfiguration
@Import({ MyConfig.class, MyAnotherConfig.class })
public class Application {

    public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
    }

}
```

위의 예제에서는 @Component 이나 @ConfigurationProperties 어노테이션이 붙은 클래스들을 자동으로 빈으로 주입하지 않고, 유저가 @Import 에 정의한 빈만 임포트 된다.

이런식으로 @SpringBootApplication 이 아니라 필요한 어노테이션만 가져다가 사용해도 된다.

# 7. Running Your Application

애플리케이션을 jar 로 패키징하고 내장된 HTTP 서버를 사용하는데 있어 가장 큰 장점은 여느때와 마찬가지로 애플리케이션을 실행할 수 있다는 점이다.

[스프링 부트 자동 설정](https://www.notion.so/34ee4dda5cd64638a3e5d470d7c9721d)

1. Running from an IDE
2. Running as a Packaged Application

    Maven 이나 Gradle 을 이용해서 실행가능한 jar 파일을 만들고, 이를 java -jar 로 실행할 수 이다.

    ```java
    $ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
    ```

3. Using the Maven Plugin

    Spring Boot Maven 플러그인은 run 이라는 goal 을 사용해 애플리케이션을 빠르게 ㅓㅁ파일하고 실행한다.

    ```java
    $ mvn spring-boot:run
    ```

4. Using the Gradle Plugin

    Spring boot Gradle 플러그인은 bootRun 이라는 task 로 애플리케이션을 실행할 수 있다.

    ```java
    $ gradle bootRun
    ```

5. Hot Swapping

    스프링 부트 어플리케이션도 평범한 자바 어플리케이션이므로 JVM hot-swapping 기능을 별다른 추가적인 설치나 설정 없이 사용할 수 있다.

    `spring-boot-devtools` 모듈은 빠른 애플리케이션 재시작을 지원하는데 이때 hot swapping 이 이용된다.

    - 정적 리소스 리로드
    - 컨테이너 재시작 없이 템플릿 리로드
    - 컨테이너 재시작 없이 자바 클래스 리로드

# 8. Developer Tools

스프링 부트는 개발을 좀 더 즐겁게 해주기 툴들을 제공한다.

spring-boot-devtools 모듈을 이용하면

- 변경된 템플릿을 바로 바로 확인할 수 있도록 디폴트로 캐싱 옵션을 사용하지 않고
- 클래스 패스 상의 파일이 바뀔 때마다 자동으로 재시작 하며
- 어플리케이션 재시작 시마다 condition evaluation delat 보고서를 기록
- 정적 자원의 경우 프로그램 재시작이 필요 없이 바로 리로드 될 수 있도록

하는 등 개발에 편리한 기능을 사용할 수 있다.

# 9. Packaging Your Application for Production

- 실행가능한 jar 는 제품 배포에 사용 될 수 있다.
- 이들은 독립적(self-contained)이기에 클라우드 기반 배포에 적합하다
# Using Spring boot - 2

<br>

[Using Spring Boot](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection)

# Spring Beans and Dependency Injection

Spring Framework의 테크닉들을 사용해 beans를 정의하고, 필요한 경우 주입할 수 있다.

`@ComponentScan` , `@Autowired` 를 사용하자.

- Root package의 application class 코드에서 `@ComponentScan` 을 사용하면, 작성한 application components (`@Component`, `@Service`, `@Repository`, `@Controller` etc.) 들을 자동으로 Spring Beans로 등록해준다.

예시 코드를 보자.

- `@Service` Bean을 생성하고, 생성자를 통해 현재 필요한 `RiskAssessor` Bean을 주입 받아보자.

```java
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

- 생성자가 하나라면, `@Autowired` 는 생략할 수 있다.

<br>

<br>


# Using the `@SpringBootApplication` Annotation

많은 Spring Boot 개발자들이 그들의 "application class"에 `auto-configuration`, `component-scan`, `extra configuration 정의` 이 되길 원하는데, 단지 하나의 어노테이션인  `@SpringBootApplication` 를 사용하면 앞 3가지 기능을 적용할 수 있다.

<br>


**어떻게 가능하지~!?**

 [앞 문서](https://github.com/Meet-Coder-Study/reference-documentation-study/blob/main/spirng-boot-2.4.x/week3/A%ED%8C%80_%EA%B9%80%EB%B3%B4%EB%B0%B0_Using_Spring_Boot_1.md)에서도 나와있듯 `@SpringBootApplication` 은 아래 3가지 어노테이션으로 이루어져있기 때문이다.

- `@EnableAutoConfiguration` : Spring Boot의 auto-configuration 매커니즘 실행
- `@ComponentScan` : 어플리케이션이 존재하는 패키지에서  `@Component`  를 스캔
- `@Configuration` : Context에서 Beans를 추가할 수 있게 하거나 또는 다른 추가적인 configuration classes들을 import할 수 있게 허용함

`@SpringBootApplication` 사용은 필수적인 것이 아니다. 해당 기능을 수행하는 다른 방법으로도 수행이 가능하다. 
예를 들어, Component-scan을 수행하기 싫다면 `@SpringBootApplication` 을 사용하지 않고 위 3개의 어노테이션 중 `@ComponentScan` 을 제외한 어노테이션을 사용하면 된다.

<br>

예시를 들어보자.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.context.annotation.ComponentScan
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration(proxyBeanMethods = false)
@EnableAutoConfiguration
@Import({ MyConfig.class, MyAnotherConfig.class })
public class Application {

    public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
    }

}

```

- 위 Spring Boot application은
    - `@Component` annotated 클래스
    - `@ConfigurationProperties` annotated 클래스
- 들은 자동으로 detect되지 않는다.

그것을 제외하고 구동되는 것 빼곤 기존에 작동하던 것(`@SpringBootApplication` 사용)과 똑같이 작동한다.

- 따로 정의되길 원하는 bean들은 위 코드처럼 `@Import` 을 사용해 정의할 수 있다.

<br>
<br>


## `@Configuration(proxyBeanMethods = false)` ?

예시 코드 보다가 본 코드.

`Configuration` 은 알겠는데 `proxyBeanMethods` 는 무엇일까?

[Configuration (Spring Framework 5.3.6 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html#proxyBeanMethods--)

<br>

말그대로 "Bean method를 proxy 할 것인가"를 설정할 수 있다.

[Bean (Spring Framework 5.3.6 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)

<br>

- 기본 값인 `true` 로 하게 되면, [CGLIB](https://github.com/cglib/cglib) 를 통해 인터셉트되어 `@Bean` 메서드들이 프록시 된다.
    - 해당 `@Bean` 메서드들이 call 될 때 프록시를 통해 싱글톤 scope의 bean이 전달된다.
    <br>


    - 메서드가 call 될 때마다 같은 인스턴스가 전달된다.
    <br>


    - inter-bean reference(method)의 사용이 가능하다.

        ```java
        @Configuration(proxyBeanMethods = true)
        public class AppConfiguration {

            // inter-bean reference -> print "CService is made !" only once

            @Bean
            AService aService() {
                return new AService(sharedService());
            }

            @Bean
            BService bService() {
                return new BService(sharedService());
            }

            @Bean
            CService sharedService() {
                // This constructor prints "CService is made !"
                return new CService();
            }
        }
        ```

<br>
<br>


- `false`로 하게 되면, 프록시 되지 않는다.
    - 메서드가 call 될 때마다 새로운 bean 인스턴스가 만들어진다. 즉, Factory method처럼 행동하게 된다.
    <br>

    - `@Configuration` 어노테이션이 사용되지 않은 곳에서 `@Bean` 메서드를 만들었을 때와 같이 행동하며, 일반적으로 `Bean Lite Mode` 라고 불린다.
    <br>

    - Performance를 향상시킬 수 있다 → configuration class에 대해서 proxy들을 생성하지 않기 때문에 Spring의 startup time을 줄일 수 있고, 메모리 사용률 또한 줄일 수 있다.
    <br>

    - Spring native (GraalVM 기반)에서는 dyunamic class loading을 지원하지 않기 때문에 무조건 `proxyBeanMethods = false` 로 작동하게 된다.
        - 만약 사용하면 다음과 같은 오류를 보게된다.

            ```bash
            java.lang.IllegalStateException: ERROR: in 'training.spring.samples.NativeApplication' these methods are directly invoking methods marked @Bean: [sharedDependency] - due to the enforced proxyBeanMethods=false for components in a native-image, please consider refactoring to use instance injection. If you are confident this is not going to affect your application, you may turn this check off using -Dspring.native.verify=false.
            ```

<br>
<br>


## Reference

[When to set proxyBeanMethods to false in Springs @Configuration?](https://stackoverflow.com/questions/61266792/when-to-set-proxybeanmethods-to-false-in-springs-configuration)

[Understanding inter-bean method reference in Spring configuration](https://spring.training/understanding-inter-bean-method-reference-in-spring-configuration/)

[Spring @Bean 애노테이션](https://johngrib.github.io/wiki/spring-annotation-bean/)

[Configuration (Spring Framework 5.3.6 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html#proxyBeanMethods--)
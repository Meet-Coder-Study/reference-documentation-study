# 29. Creating Your Own Auto-configuration
--

Spring Boot의 auto-configuration은 추가한 jar 파일에 따라 설정 정보들을 자동으로 설정 한다. 
또한, 자신만의 설정을 만들어서 제공할 수 도 있다.

auto-configuration은 auto-configuration code와 함께 사용할 일반적인 라이브러리를 제공하는 "starter"와 연관될 수 있다. 
먼저 자체적으로 auto-configuration을 구축하기 위해서 [알아야 할 사항을 다루고 사용자 지정 starter를 생성하는 데 필요한 일반적인 단계](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-custom-starter)로 이동한다.

## 29.1 Auto-configuration Bean

Auto-configuration을 사용하고 싶다면 ``@EnableAutoConfiguration`` 또는 ``@SpringBootApplication``를 사용하면 된다.
Auto-configuration Class는 표준 Configuration Class들로 구현되고 해당 Class에서는 @ConditionalOnClass및 @ConditionalOnMissingBean annotation을 사용한다. 
추가적으로 @Conditional annotation은 auto-configuration을 적용 할 시기를 제한하는데 사용된다.
이렇게하면 관련 클래스가 발견되고 자체 @Configuration을 선언하지 않은 경우에만 자동 구성이 적용된다.

spring-boot-autoconfigure의 소스 코드를 탐색하여 Spring이 제공하는 @Configuration 클래스를 볼 수 있다.([META-INF/spring.factories](https://github.com/spring-projects/spring-boot/blob/v2.4.3/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories) 파일 참조)

## 29.2 Locating Auto-configuration Candidates

Spring Boot는 META-INF/spring.factories에 published 된 jar 내에 파일이 있는지 확인한다. 
파일은 다음 예제와 같이 EnableAutoConfiguration 키 아래에 구성 클래스를 나열해야합니다.

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration = \
com.mycorp.libx.autoconfigure.LibXAutoConfiguration, \
com.mycorp.libx.autoconfigure.LibXWebAutoConfiguration
```

> Auto-configurations는 반드시 위와 같은 방법으로만 로딩되어야한다. 
> 특정 패키지 공간에 정의되어 있고 구성 요소 스캔의 대상이 아닌지 확인한다.
> 또한, 자동 구성 클래스는 추가 구성 요소를 찾기 위해 구성 요소 검색을 사용하지 않아야한다.
> 대신 특정 ``@Imports``를 사용해야한다.

configuration을 특정 순서로 설정되게 해야하는 경우는 ``@AutoConfigureAfter`` 또는 ``@AutoConfigureBefore`` 를 사용할 수 있다.
예를 들어, 웹 관련 구성을 제공하는 경우 WebMvcAutoConfiguration 후에 클래스를(자신만의 설정) 적용해야 할 수 있다.

서로에 대한 직접적인 지식이 없어야하는 특정한 Auto-configuation의 순서를 정하고 싶을 때 @AutoConfigureOrder를 사용할 수도 있다. 
이 annotation은 일반 @Order 주석과 동일한 의미를 갖지만 Auto-configuation 클래스에 대한 전용 순서를 제공한다.

표준 @Configuration 클래스와 마찬가지로 Auto-configuation 클래스가 적용되는 순서는 해당 Bean이 정의 된 순서에만 영향을준다. 
이러한 Bean이 이후에 생성되는 순서는 영향을받지 않으며 각 Bean의 종속성 및 @DependsOn 관계에 의해 결정된다.

## 29.3 Condition Annotations

거의 항상 Auto-configuration 클래스에 하나 또는 그 이상의 @Conditional을 포함하려고한다. 
보통 @ConditionalOnMissingBean은 개발자가 기본값에 만족하지 않는 경우 Auto-configuration을 재정의 하는데 사용된다.

Spring Boot에는 @Configuration 클래스 또는 개별 @Bean 메서드에 주석을 추가하여 자신의 코드에서 재사용 할 수있는 여러 @Conditional 주석이 포함되어 있다.    
아래와 같은 주석들이 있다.

- [Class Conditions](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-class-conditions)

- [Bean Conditions](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-bean-conditions)

- [Property Conditions](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-property-conditions)

- [Resource Conditions](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-resource-conditions)

- [Web Application Conditions](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-web-application-conditions)

- [SpEL Expression Conditions](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-spel-conditions)

## 29.4 Testing your Auto-configuration

Auto-configuration은 사용자 구성(@Bean 정의 및 환경 사용자 정의), 조건 평가(특정 라이브러리의 존재) 등 여러 요인의 영향을 받을 수 있다.
구체적으로 각 테스트는 이러한 사용자 정의의 조합을 나타내는 잘 정의 된 ApplicationContext를 만들어야한다.
ApplicationContextRunner 이를 달성하는 좋은 방법을 제공한다.

ApplicationContextRunner 일반적으로 기본 공통 구성을 수집 하기 위한 테스트 클래스의 필드로 정의된다. 
다음 예제 UserServiceAutoConfiguration는 항상 호출 되는지 확인한다.

```java
private final ApplicationContextRunner contextRunner = new ApplicationContextRunner()
        .withConfiguration(AutoConfigurations.of(UserServiceAutoConfiguration.class));
```

각 테스트는 실행기를 사용하여 특정 사용 사례를 나타낼 수 있다. 
예를 들어 아래 샘플은 UserConfiguration을 호출하고 Auto-configuration이 제대로 백 오프 되는지 확인한다. 
실행을 호출하면 AssertJ와 함께 사용할 수있는 콜백 컨텍스트가 제공된다.

```java
@Test
void defaultServiceBacksOff() {
    this.contextRunner.withUserConfiguration(UserConfiguration.class).run((context) -> {
    //제대로 실행되면 이 내부 코드가 실행됨.
        assertThat(context).hasSingleBean(UserService.class);
        assertThat(context).getBean("myUserService").isSameAs(context.getBean(UserService.class));
    });
}

@Configuration(proxyBeanMethods = false)
static class UserConfiguration {

    @Bean
    UserService myUserService() {
        return new UserService("mine");
    }

}
```

``Environment``도 쉽게 아래와 같이 사용자 지정할 수 있다.

```java
@Test
void serviceNameCanBeConfigured() {
    this.contextRunner.withPropertyValues("user.name=test123").run((context) -> {
        assertThat(context).hasSingleBean(UserService.class);
        assertThat(context.getBean(UserService.class).getName()).isEqualTo("test123");
    });
}
```


러너를 사용하여 ``ConditionEvaluationReport``에 표시 할 수도 있다. 
보고서는 INFO 또는 DEBUG 수준만 표시될 수 있다.
다음 예제는 ConditionEvaluationReportLoggingListener를 사용하여 auto-configuration 테스트에서 보고서를 인쇄하는 방법을 보여준다.

```java
@Test
void autoConfigTest() {
    ConditionEvaluationReportLoggingListener initializer = new ConditionEvaluationReportLoggingListener(
            LogLevel.INFO); // level can be only INFO OR DEBUG
    ApplicationContextRunner contextRunner = new ApplicationContextRunner()
            .withInitializer(initializer).run((context) -> {
                    // Do something...
            });
}
```

### 29.4.1 Simulating a Web Context
Servlet 또는 Reactive web application context에서만 동작하는 auto-configuration 테스트를 하고싶다면 ``WebApplicationContextRunner`` 또는 ``ReactiveWebApplicationContextRunner`` 주석을 사용하면 된다.

### 29.4.2 Overriding the Classpath

특정 클래스 또는 패키지가 런타임에 존재하지 않을 때 어떤 일이 발생하는지 테스트 할 수도 있다. 
Spring Boot는 runner가 쉽게 사용할 수있는 ``FilteredClassLoader``와 함께 제공됩니다. 
다음은 ``UserService``가 없으면 auto-configuration이 비활성화 되어있음을 보여주는 테스트코드이다.

```java
@Test
void serviceIsIgnoredIfLibraryIsNotPresent() {
    this.contextRunner.withClassLoader(new FilteredClassLoader(UserService.class))
            .run((context) -> assertThat(context).doesNotHaveBean("userService"));
}
```

## 29.5 Creating Your Own Starter

custom starter에는 아래의 항목을 포함시킬 수 있다.

 - auto-configuration code가 포함 된 autoconfigure module.
   
 - autoconfigure module 뿐만 아니라 라이브러리 및 일반적으로 유용한 추가 dependency에 대한 dependency을 제공하는 starter module입니다. 
 간단히 말해서, 스타터를 추가하면 해당 라이브러리 사용을 시작하는 데 필요한 모든 것이 제공됩니다.
 
### 29.5.1. Naming

다른 Maven groupId를 사용하더라도 모듈 이름을 spring-boot로 시작하면 안된다(Spring boot에서 같은 이름으로 auto-configuration을 제공 할 수도 있다.)

모듈 이름을 정하면, 먼저 모듈이름을 적고 그 뒤에 starter를 붙여야한다.
(ex. ``acme``에 대한 start를 작성하고, auto-configuration 모듈 이름을 ``acme-spring-boot``라고 하면 starter의 이름은 ``acme-spring-boot-start``가 되어야 한다.)

### 29.5.2. Configuration keys

스타터가 설정에 대한 키를 지원한다면 스프링 부트가 사용하는 네임 스페이스(예 : server, management, spring 등)를 키로 사용하면 안된다.
configuration key를 제공하려면 모든 key 앞에 고유한 namespace를(예 : acme) 접두어로 사용해야 한다.

다음 예제와 같이 각 특성에 대해 javadoc 필드를 추가하여 구성 키가 문서화되어 있는지 확인하십시오.

```java
@ConfigurationProperties("acme")
public class AcmeProperties { //접두어로 acme 사용한 예제

    /**
     * Whether to check the location of acme resources.
     */
    private boolean checkLocation = true;

    /**
     * Timeout for establishing a connection to the acme server.
     */
    private Duration loginTimeout = Duration.ofSeconds(3);

    // getters & setters

}
```

> ``@ConfigurationProperties`` JSON에 추가되기 전에 처리되지 않으므로 Javadoc 필드 에는 일반 텍스트만 사용해야 한다.

다음은 설명의 일관성을 유지하기 위해 내부적으로 따르는 몇 가지 규칙입니다.

 - “The”또는 “A”로 설명을 시작하지 마십시오.
 - boolean type의 경우 “Whether”또는 “Enable”로 설명을 시작하십시오.
 - 콜렉션 기반 type의 경우 “쉼표로 구분 된 목록”으로 설명을 시작하십시오.
 - long 대신 java.time.Duration을 사용하고 밀리 초와 다른 경우 기본 단위를 설명하십시오 (예 : “지속 기간 접미사가 지정되지 않으면 초가 사용됩니다.”
 - 런타임시 판별해야하는 경우가 아니면 설명에 기본값을 제공하지 마십시오.

키에 대해서도 IDE 지원을 사용할 수 있도록 메타 데이터 생성 을 트리거 해야합니다. 
생성 된 메타 데이터를( META-INF/spring-configuration-metadata.json) 검토하여 키가 올바르게 문서화되었는지 확인할 수 있습니다. 
호환 가능한 IDE에서 자체 스타터를 사용하는 것도 메타 데이터의 품질을 검증하는 데 좋은 생각입니다

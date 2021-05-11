# **Auto-Configuration**

<br>
<br>

✔️ Spring Boot에서의 <code>**auto-configuration**</code>은 ComponentScan을 통해 Component를 찾아 Bean으로 생성한 뒤, **추가적인 Bean들을 함께 생성**해준다.

<br>
<br>

## **1. @SpringBootApplication**

<br>

**1.1 @SpringBootApplication**
- 이 annotation이 선언된 class는 main method를 포함하여 spring boot의 실행 기준이며, 가장 기본적인 설정을 대신 선언해주는 annotation이다.

<br>

```java
@EnableJpaAuditing
@SpringBootApplication
public class Application {

    @PostConstruct
    void started() {
        TimeZone.setDefault(TimeZone.getTimeZone("Asia/Seoul"));
    }

    public static void main(String[] args) {
        SpringApplication.run(CoffzagApplication.class, args);
    }

}
```

<br>
<br>

### **1.2 @SpringBootApplication 선언 내용**
- **<code>@SpringBootApplication</code>** 안에 다음 annotation을 포함해 여러 annotation들이 선언되어 있다.
- **<code>@SpringBootConfiguration</code>**
- **<code>@ComponenetScan</code>**
- **<code>@EnableAutoConfiguration</code>**

<br>

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
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

<br>
<br>

### **🔹 @SpringBootConfiguration == @Configuration**
- <code>@Configuration</code>이랑 같은 역할이며, <code>환경설정 Bean class(Spring Bean 설정 class)</code>를 표현하기 위해 사용한다.
- <code>@SpringBootConfiguration</code>라고 표현하는 이유는, Spring Boot 환경설정 class임을 표현하기 위해서이다.
- 이름만 변경한 것이기 때문에, <code>@Configuration으로 변경하여 실행해도 결과가 동일</code>하다.
- <code>@ComponentScan</code>이 처리 될 때, <code>@Configuration</code> 뿐만 아니라 <code>그 class에  @Bean으로 설정된 Bean들도 초기화</code> 한다.

<br>
<br>

### **🔹 @CompoenetScan**
- **<code>Spring Container 초기화 관련 annotation(1)</code>**
- <code>@ComponentScan</code> annotaion이 현 패키지에서 <code>@Componenet</code> annotation이 붙어 있는 class들을 찾아서, <code>Bean</code>*으로 등록한다.
- 본 과정에서 **직접 설정한 Bean들**도 함께 자동 생성된다.*
- **직접 설정한 Bean들**: <code>@Controller</code>, <code>@RestController</code>, <code>@Service</code>, <code>@Repository</code>, <code>@Configuration에 등록한 @Bean</code> 
- Spring Boot에서는 위 Bean들이 자동으로 생성되기 때문에 <code>ThreadPoolTaskExecutor</code>*를 사용할 수 있다.
<br>
*<code>Bean(빈)</code>: spring에서 관리하는 POJO(Plain Old Java Object)
<br>
*.excludeFilter에 해당하는 class는 제외<br>
*<code>ThreadPoolTaskExecutor</code>: thread pool을 사용하는 executor로, 쉽게 multi-thread를 구현할 수 있도록 하는 class <br>
➡️ 참고: https://blog.outsider.ne.kr/1066

<br>
<br>

### **🔹 @EnableAutoConfiguration**
- **<code>Spring Container 초기화 관련 annotation(2)</code>**
- <code>Auto Configuration</code>을 사용하겠다는 의미이다.
- Spring Boot가 spring.factories(meta file)에 <code>사전에 정의한 AutoConfiguration 내용에 의해 Bean을 생성</code>한다.
- 다음 예제와 같이 <code>@ComponentScan</code>과 함께 사용된다.

<br>

```java
@SpringBootConfiguration
@ComponentScan("com.mini.coffzag")
@EnableAutoConfiguration
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

<br>
<br>

> 📌 ComponentScan으로 등록되는 Bean과 Spring Boot가 정의한 Bean이 충돌하진 않을까? <br><br>
**"Spring Boot는 Confition interface와 @Conditional을 이용해 이러한 문제를 해결하여 AutoConfiguration 기능을 제공해준다."**


<br>
<br>

## **✔️ 2. Auto-Configuration**
<br>

> **Spring Boot가 미리 정의해둔 AutoConfiguration 정보**<br>
➡️ File: <code>spring-boot-autoconfigure/META-INF/spring.factories</code><br>
➡️ Github: https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories

<br>
<br>

### **2.1 AutoConfiguration Filters & Conditions**

- AutoConfiguration 정보는 아래와 같이 상당히 많이 등록되어 있다.
- 따라서 각 AutoConfiguration이 필요한 상황에만 실행될 수 있도록 annotaion으로 관리한다.
- @Conditional와 Condition 인터페이스

<br>

```java
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
...(생략)
```

<br>

### **2.1 Auto Configuration Import Filters**
- spring.factories 정보 중 AutoConfigurationImportFilter 관련 설정이다.
- 다음 3개의 filter가 적용되어 있다.

<br>

```java
# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
org.springframework.boot.autoconfigure.condition.OnClassCondition,\
org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition
```

<br>

### **🔹 @OnBeanCondition**
- 특정 <code>**Bean**</code>들의 존재유무에 대해서 다루는 filter
- 대상 Bean: @ConditionalOnBean, @ConditionalOnMissingBean, @ConditionalOnSingleCandidate

<br>

### **🔹 @OnClassCondition**
- 특정 <code>**Class**</code>들의 존재유무에 대해서 다루는 filter
- 대상 Class: @ConditionalOnClass, @ConditionalOnMissingClass

<br>

### **🔹 @OnWebAplicationCondition**
- 특정 <code>**WepApplicationContext**</code>의 존재유무에 대해서 다루는 filter
- 대상 WebApplication: @ConditionalOnWebApplication, @ConditionalOnNotWebApplication

<br>

*각 filter와 대상 annotation의 자세한 내용<br>
➡️ 참고: http://dveamer.github.io/backend/SpringBootAutoConfiguration.html

<br>
<br>

### **2.2 Exclude Auto Configuration Import Filters**
- 특정 AutoConfiguration을 사용하지 않고자 할 때 exclude 속성으로 명시한다.

<br>

```java
// 예제: DataSourceAutoConfiguration.class의 AutoConfiguration을 exclude
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
    ...(생략)
}
```

<br>
<br>

## **🔴 @SpringBootApplication**
- 위에서 살펴본 바와 같이 <code>@SpringBootApplication</code>에는 <code>@ComponentScan</code>과 <code>@EnableAutoConfiguration</code>을 포함한다.<br>
- **즉, <code>SomeApplication.java</code>에 <code>@SpringBootApplication</code> annotation만 설정하면, '프로젝트의 cofiguration 관련 정보들이 모두 유효하도록 설정'된다.**

<br>

```java
@SpringBootApplication
public class SomeApplication {
    public static void main(String[] args) {
        SpringApplication.run(SomeApplication.class, args);
    }
}
```

<br>
<br>

**Reference**<br>
[Spring Boot docs 2.4.3] <br>
➡️ https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-configuration-classes <br>
➡️ https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-auto-configuration <br>
<br>
[blog] <br>
➡️ http://dveamer.github.io/backend/SpringBootAutoConfiguration.html <br>
➡️ https://donghyeon.dev/spring/2020/08/01/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%EC%9D%98-AutoConfiguration%EC%9D%98-%EC%9B%90%EB%A6%AC-%EB%B0%8F-%EB%A7%8C%EB%93%A4%EC%96%B4-%EB%B3%B4%EA%B8%B0/<br>
➡️ https://jhleed.tistory.com/126

<br>
<br>
## @SpringBootApplication 어노테이션 살펴보기

@SpringBootApplication는 어떻게 정의 되어 있을까?

어노테이션의 클래스 레벨에 붙은 어노테이션을 살펴보자.

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
  ...
}
```

- @Target(ElementType.TYPE)은 *class, interface, annotation, enum*에만 적용 가능 한 어노테이션임을 말해준다.
- @Retention(RetentionPolicy.RUNTIME)은 이 어노테이션이 컴파일 시점 후인 런타임 시점에도 정의되어 있는지를 의미한다.
- @Documented는 문서화를 위한 어노테이션이다.
- @Inherited는 이 어노테이션을 사용하는 부모클래스를 상속하는 자식 클래스도 적용됨을 의미한다.
- @SpringBootConfiguration에는 우리가 스프링 프레임워크에서도 사용했던 @Configuration 어노테이션이 정의되어 있다.
  - 이 어노테이션에는 `proxyBeanMethods` 프로퍼티가 있다. 
  - 기존의 cglib을 사용하지 않는 설정을 위해 스프링 부트 2.2이후로 추가되었다.
- @EnableAutoConfiguration는 자동설정을 사용함을 의미한다.
  - 이 어노테이션에는 `@Import(AutoConfigurationImportSelector.class)` 가 정의 되어 있다. 
- @ComponentScan은 빈을 탐색한다는 뜻이다. 
  - 필터 타입 중에서도 CUSTOM 필터 중 TypeExcludeFilter는 스캔 대상에서 제외한다.
  - 또한, AutoConfigurationExcludeFilter의 클래스도 제외된다. 
  - 이 타입은 AutoConfiguration을 수행하는 AutoConfiguration 자체를 빈 스캐닝에서 제외한다는 것을 말한다.



컴포넌트 스캔을 사용하지 않고 빈을 등록하려면 어떻게 해야할까?

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

`Application` 클래스는 일반적인 스프링부트 애플리케이션에서 @Component와 @ConfigurationProperties을 자동으로 감지하지 않는다.

빈을 수동으로 적용하기 위해 @Import 어노테이션을 사용하여 MyConfig와 MyAnotherConfig 설정 빈을 등록했다.






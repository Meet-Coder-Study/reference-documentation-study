# Profile

## @Profile
 - @Component, @Configuration, @ConfigurationProperties 이 붙은 클래스에 같이 사용 가능하다.
 ```java
@Configuration
public class HelloServiceConfig {

  @Bean
  @Profile("default")
  HelloService defaultHelloService() {
    return new DefaultHelloService();
  }
  @Bean
  @Profile({"dev", "prod"})
  HelloService worldHelloService() {
    return new WorldHelloService();
  }
}
```
 - 위의 코드는, default 환경일 때에는 defaultHelloService 를 빈으로 등록한다.
 - dev, prod 환경일 때에는 worldHelloService 를 빈으로 등록한다.
 - 환경에 따라 등록되는 빈을 정의할 수 있다는 것이다.
 
 
## 환경 정하기
 - spring.profiles.active 옵션을 통해 활성화시킬 환경을 정할 수 있다.
 ```yaml
// application.properties
spring.profiles.active=dev,prod

// yaml
spring:
  profiles:
    active: "dev,prod"
```
 - 위와 같은 설정을 해주지 않으면 기본적으로 default 라는 이름의 환경으로 돌아간다.
 
 ```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
    }
}
```
`[]
[default]`

```
spring.profiles.active=dev,prod 설정 이후
```

```java
[dev, prod]
[default]
```

<br>

## 프로필 그룹핑
 - 프로필이 세분화 되어 있으면, 같이 사용하는 프로필 끼리 그룹을 지어 놓으면 사용하기 편리할 것이다.
 - 여러가지 프로필을 production 이라는 이름의 그룹으로 만들어보자.
 ```java
// application.properties
spring.profiles.group.production[0]=proddb
spring.profiles.group.production[1]=prodmq

// yaml
spring:
  profiles:
    group:
      production:
      - "proddb"
      - "prodmq"
```

 - 이러면 이제 프로필을 활성화시킬 때 아래와 같이 그룹 이름으로 활성화 할 수 있다.
 `spring.profiles.active=production`
 
 ## @ConfigurationProperties 사용법
 
  1. 환경 파일로 관리할 클래스를 만든다.
     - `@Component` 에너테이션으로 빈으로 등록할 수 있게 한다.
     - `@ConfigurationProperties("이름")` 으로 이름을 지정한다.
     - Setter 를 만든다.
  ```java
 @Profile("active")
 @Component
 @ConfigurationProperties("student")
 public class Student {
 
     private String name;
     private int age;
 
     public String getName() {
         return name;
     }
 
     public void setName(String name) {
         System.out.println(name);
         this.name = name;
     }
 
     public int getAge() {
         return age;
     }
 
     public void setAge(int age) {
         System.out.println(age);
         this.age = age;
     }
 }
 ```
 
  2. ~Application 클래스에 `@EnableConfigurationProperties` 를 붙인다.
  ```java
 @EnableConfigurationProperties
 @SpringBootApplication
 public class DemoApplication {
 
     public static void main(String[] args) {
         SpringApplication.run(DemoApplication.class, args);
     }
 }
 ```
 
 3. application.properties 파일에 지정할 값을 아래와 같은 형식으로 작성한다.
     - 클래스에서 이름을 student 로 하였으니, student 를 앞에 적는다.
     - . 을 찍고 이후 필드값을 정의하고, 값을 채운다.
 ```java
 spring.profiles.active=active
         
 student.name = tomas
 student.age = ${random.int}
 ```
 
 4. setter 에 print 를 넣어서 값을 확인한다.
 ```java
 ...
 2021-05-12 10:45:58.955  INFO 40318 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.45]
 2021-05-12 10:45:58.994  INFO 40318 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
 2021-05-12 10:45:58.994  INFO 40318 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 545 ms
 119495704
 tomas
 2021-05-12 10:45:59.101  INFO 40318 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
 2021-05-12 10:45:59.226  INFO 40318 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
 2021-05-12 10:45:59.233  INFO 40318 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 1.045 seconds (JVM running for 1.463)
 ```



<br>

# Spring MVC

## MVC Auto-Configuration
 - `ContentNegotiatingViewResolver`, `BeanNameViewResolver` 빈을 포함한다.
    - ContentNegotiatingViewResolver : View 를 찾기 위해 요청 URL 의 확장자와 AcceptHeader 를 사용하는 ViewResolver
        - order : 등록된 View Resolver 중 우선순위를 등록가능
        - mediaTypes : 컨텐츠의 타입들을 정의 가능
        - defaultViews : 컨텐츠 타입마다의 디폴트 view 를 정의 가능
    - BeanNameViewResolver : 물리적인 뷰의 위치를 지정해주는 빈
        - 기본 위치는 /WEB-INF/views (Spring)
        - 뷰의 위치를 다른 곳에 정의해야할 때 사용 가능.
 - 정적 파일 지원
 - `Converter`, `GenericConverter`, `Formatter` 빈 등록
    - `Converter<source, target>` :  인터페이스, source 는 바꾸기 전 데이터 타입, target 은 바꿀 데이터 타입으로 지정.
      1. 컨버터 구현
        ```java
           public class EventConverter {
           
               // String을 Event 타입으로
               public static class StringToEventConverter implements Converter<String, Event> {
           
                   @Override
                   public Event convert(String s) {
                       return new Event(Integer.parseInt(s));
                   }
               }
           
               // Event 타입을 String으로
               public static class EventToStringConverter implements Converter<Event, String> {
           
                   @Override
                   public String convert(Event event) {
                       return event.getId().toString();
                   }
               }
           }
        ```       
      1. @Configuration 클래스에 컨버터 등록
        ```java
            @Configuration
            public class WebConfig implements WebMvcConfigurer {
            
                @Override
                public void addFormatters(FormatterRegistry formatterRegistry) {
                    formatterRegistry.addConverter(new EventConverter.StringToEventConverter());
                }
            
            }
        ```
    
      1. Event 구현
        ```java
            @Setter
            @Getter
            public class Event {

                private Integer id;
                
                private String title;
                
                public Event(Integer id) {
                    this.id = id;
                }
                
            }
        ```
        
      1. 컨트롤러에서 변환해서 받음
        ```java
          예를 들어 /event/1 라는 요청이 들어오면, Converter 로 등록된 StringToEventConverter 가 동작.
          StringToEventConverter 는 인자로 받아온 1 로 Event 객체를 만들어서 줌.
          따라서 컨트롤러의 인수에서 Event 라는 객체로 받을 수 있음.
      
          @GetMapping("/event/{event}")
          public String getEvent(@PathVariable Event event) {
          }
        ```
    - Formatter : Web 에 특화. Converter 와는 다르게 Object 와 *String* 으로 변환 타입이 고정되어 있다.
      - 따라서 제네릭으로 Object 쪽의 하나의 인자만 받는다.
 - `HttpMessageConverters` 지원
 - `MessageCodesResolver` 지원
 - `index.html` 정적 파일 지원
 - `Favicon` : 커스텀 파비콘 지원
 - `ConfigurableWebBindingInitializer` 빈을 사용

<br>

## Web MVC 내 입맛에 맞게 설정하기
 1. 만약 몇가지 MVC 설정을 커스터마이징하고 싶다  
   -> `WebMvcConfigurer` 를 구현한 @Configuration 이 붙은 클래스에 추가.  
   -> @EnableWebMvc 는 X  
    <br>
 1. MVC 설정을 완전히 제어하기  
   -> @EnableWebMvc 가 붙은 @Configuration 을 정의  
   -> 또는, `DelegatingWebMvcConfiguration` 을 구현 (@EnableWebMvc 에 이 속성이 있음)  
   -> 이는 WebMvc 관련 설정 전체를 아예 @EnableWebMvc(DelegatingWebMvcConfiguration)로 위임한다는 뜻.  
    <br>
 1. RequestMappingHandlerMapping, RequestMappingHandlerAdapter, ExceptionHandlerExceptionResolver 을 커스텀하게 구현하면서 기존 설정은 유지  
   -> `WebMvcRegistrations` 을 구현한 새로운 클래스를 정의하고 사용할 컴포넌트에서 사용.
    
## JSON 직렬화/역직렬화 커스텀
 - 기본적으로 Jackson 라이브러리 사용.
 - @JsonComponent 를 붙인 클래스에서 `JsonSerializer<SomeObject>`, `JsonDeserializer<SomeObject>` 를 상속한 정적 중첩클래스에서 구현하면 됨.
 - @JsonComponent 는 @Component 처럼 빈 스캔 대상임.


## 파비콘
 - static 폴더(관련 위치를 설정했다면 설정한 위치)에 `favicon.ico` 라는 파일이 있으면 파비콘을 만들어준다.

## welcome page
 - 기본적으로 설정된 static content 위치에서 index.html 파일을 찾는다.
 - 없다면, index 템플릿을 찾는다.
 - 없다면, 에플리케이션의 welcome page 를 사용한다.
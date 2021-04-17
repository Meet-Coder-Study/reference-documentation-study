# 3. Using Spring Boot 1,2,3,4

- https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html

## 3-1 Build Systems

Gradle and Maven ( coc - Configuration over Convention )

Gradle과 maven에 관련된 설명은 2장에 첨부하였으므로 해당 내용 참조

만약 maven을 쓰는 프로젝트를 하게되며 버전이 변경되었을 때 바로바로 업데이트 하기 위한 설정 tip

인텔리제이를 쓴다면 preference > maven > always update snapshots check

## 3-2 Structuring Your code

pass

## 3-3 Configurations Classes

억지이긴 하나 config에 빈을 등록하고 사용해보자

```java
@Configuration
public class TestConfig {

    @Bean
    public List<String> abc(){
        return Arrays.asList("a","b","c");
    }
}
```

```java
@RestController
@RequestMapping("/test")
public class TestController {

  @Autowired
  private List<String> abc;

  @GetMapping
  public List<String> abc() {

    ApplicationContext context = new AnnotationConfigApplicationContext(TestConfig.class);
    List abc = context.getBean("abc", List.class);
    return abc;
  }

  @GetMapping("/test")
  public List<String> abcList() {
    return abc;
  }
}
```

@Bean vs @Component

## 3-4 Auto-configuration

아래처럼 특정 컨피그를 빼는 경우가 있을 경우?? 어느 경우에 적합할것인지?

```java
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
public class MyApplication {
}
```

여러 repo에서 Common한 부분을 Config로 정의 한후 Common하게 쓰이기 위하여 nexus에 올렸다고 가정, 모든 서버에서 Common에 들어가 있는 모든 Config를 쓰지 않을수 있다.

ex) 불필요한 config들을 제거하고 사용
1. 특정 서버에서는 rdb만 쓰고 몽고디비를 안쓸 경우
2. 특정 서버에서는 db를 전혀 연결하지 않을 경우

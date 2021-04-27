# 3. Using Spring Boot 4weeks

- https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection

## 5. Spring Beans and Dependency Injection

> why? 생성자 주입을 써야하나?

예시

```java
@Service
@Slf4j
public class TempService {

    @Autowired
    private UserService userService;

    public void sayTemp() {
        log.info("say temp");
        userService.helloUser();
    }
}

@Service
@Slf4j
public class UserService {

  @Autowired private TempService tempService;

  public void helloUser() {
    log.info("hihi user");
    tempService.sayTemp();
  }
}

@RequestMapping("/temp")
@RestController
public class TempUserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public void test(){
        userService.helloUser();
    }
}

```

위처럼 코드를 작성하고
localhost:8080/temp를 호출시 아래의 로그와 같이 __StackOverflowError__가 발생한다. 즉 runtime에 발생하며 해당 서버가 죽게 된다.

```
2021-04-28 01:57:16.628  INFO 43745 --- [nio-8080-exec-1] com.pkj.boker.demo.fourth.UserService    : hihi user
2021-04-28 01:57:16.628  INFO 43745 --- [nio-8080-exec-1] com.pkj.boker.demo.fourth.TempService    : say temp
2021-04-28 01:57:16.629  INFO 43745 --- [nio-8080-exec-1] com.pkj.boker.demo.fourth.UserService    : hihi user
2021-04-28 01:57:16.629  INFO 43745 --- [nio-8080-exec-1] com.pkj.boker.demo.fourth.TempService    : say temp
2021-04-28 01:57:16.698 ERROR 43745 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Handler dispatch failed; nested exception is java.lang.StackOverflowError] with root cause

java.lang.StackOverflowError: null
```

자 위의 코드를 살짝만 바꿔보자

```java
@Service
@Slf4j
public class UserService {

//  @Autowired private TempService tempService;

    private final TempService tempService;

    public UserService(TempService tempService) {
        this.tempService = tempService;
    }

    public void helloUser() {
    log.info("hihi user");
    tempService.sayTemp();
  }
}

@Service
@Slf4j
public class TempService {

//    @Autowired
//    private UserService userService;

    private final UserService userService;

    public TempService(UserService userService) {
        this.userService = userService;
    }

    public void sayTemp() {
        log.info("say temp");
        userService.helloUser();
    }
}
```

위처럼 변경 후 코드 실행시 아래처럼 순환참조가 됨을 컴파일 시점에 미리 알수 있다.

```
***************************
APPLICATION FAILED TO START
***************************

Description:

The dependencies of some of the beans in the application context form a cycle:

┌─────┐
|  tempService defined in file [/Users/beanbroker/pkj_private_work/spring_study_2021/out/production/classes/com/pkj/boker/demo/fourth/TempService.class]
↑     ↓
|  userService defined in file [/Users/beanbroker/pkj_private_work/spring_study_2021/out/production/classes/com/pkj/boker/demo/fourth/UserService.class]
└─────┘
```

생성자 주입을 하게 될경우 아래와 같이
new TempService(new UserService(new TempService(new UserService)))

__Constructor based Injection 을 쓰자__
1. 순환 참조를 방지할 수 있다
2. immutable 하다., 실행 중에 객체가 변하는 것을 막을 수 있다.
    - 	Notice how using constructor injection lets the riskAssessor field be marked as final, indicating that it cannot be subsequently changed.


> RequiredArgsConstructor 어노테이션을 알고 가자


## 6. Using the @SpringBootApplication Annotation

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

음 여기서 @Target은 언제쓸가?

TYPE의 적용대상 클래스, 인터페이스, 열거타입
METHOD의 적용대상 method

테스트해보자

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface SampleAuth {
  int data() default 0;
}



@Component
public class SampleAuthInterceptor implements HandlerInterceptor {

  private static final String SECRET_HEADER = "X-STUDY";

  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
      throws Exception {

    if (!(handler instanceof HandlerMethod)) return true;
    HandlerMethod handlerMethod = (HandlerMethod) handler;

    if (ObjectUtils.isEmpty(getAnnotation(handlerMethod, SampleAuth.class))) {
      return true;
    }

    final String token = request.getHeader(SECRET_HEADER);
    if (token == null) {

      throw new Exception("wrong wrong");
    }
    return true;
  }

  private <A extends Annotation> A getAnnotation(
      HandlerMethod handlerMethod, Class<A> annotationType) {
    return Optional.ofNullable(handlerMethod.getMethodAnnotation(annotationType))
        .orElse(handlerMethod.getBeanType().getAnnotation(annotationType));
  }
}



@Configuration
@RequiredArgsConstructor
public class SampleAuthWebConfig implements WebMvcConfigurer {

  private final SampleAuthInterceptor sampleAuthInterceptor;

  @Override
  public void addInterceptors(InterceptorRegistry registry) {

    registry.addInterceptor(sampleAuthInterceptor).addPathPatterns("/sample/**");
  }
}



@Slf4j
@RequestMapping("/sample")
@RestController
@SampleAuth
public class SampleController {

  @GetMapping("/auth")
  public void test() {

    log.info("test pass");
  }
}

```

http://localhost:8080/sample/auth 를 실행하게 될 경우 예외가 발생한다. header에 X-STUDY가 없기 때문에!!
SampleController에 @SampleAuth가 붙었고 해당 부분을 interceptor에서 검사를 한다. 해당 부분은 직접 디버깅을 해보자

음 이를 어드쓰면 좋을가라고 고민했을때? jwt token으로 통신을 하였을때 token을 복호화 한 후 role에 맞게 처리할수 있다고 가정한다면 편하지 않을가? 라는 생각을 해보자


JAVA_OPTS

- Xms : 초기 Heap Size (init, default 64m)
- Xmx : 최대 Heap Size (Max,  default 256m)
- XX:+UseSerialGC : Serial GC
- XX:+UseParallelGC : Parallel GC
- XX:+UseParNewGC : CMS GC
- XX:+UseG1GC : G1GC

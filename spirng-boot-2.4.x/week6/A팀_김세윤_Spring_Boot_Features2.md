# [6주차] Profiles / Logging / Internationalization / JSON / Developing Web Application

## Prifiles

- 특정 환경에서만 환경설정을 사용할 수 있도록 Profiles를 제공합니다.

### 사용 방법

1. @Profiles("profiles-name") 작성
2. [spring.profiles.active](http://spring.profiles.active)="profiles-name" 속성값 사용

### Group 지정

- spring.profiles.group.profile-name[index]을 이용해 그룹을 설정할 수 있습니다.

```jsx
spring.profiles.group.production[0]=proddb
spring.profiles.group.production[1]=prodmq
```

### Profiles 실행 방법

- `SpringApplication.setAdditionalProfiles(…)` 을 이용해 실행 시, 추가할 프로파일을 설정할 수 있습니다.
- `--spring.profiles.active=dev,hsqldd` 를 사용해 실행 시 Commend Line으로 설정이 가능합니다.

## Logging

- Spring Boot는 기본적으로 로그를 설정해놓습니다. Log4j2, Logback에 대한 기본 구성을 설정할 수 있습니다.
- 기본 로그는 아래와 같습니다.

```jsx
2019-03-05 10 : 57 : 51.112 정보 45469 --- [main] org.apache.catalina.core.StandardEngine : 서블릿 엔진 시작 : Apache Tomcat / 7.0.52
2019-03-05 10 : 57 : 51.253 정보 45469 --- [ost-startStop-1] oaccC [Tomcat]. [localhost]. [/] : Spring 임베디드 WebApplicationContext 초기화
2019-03-05 10 : 57 : 51.253 정보 45469 --- [ost-startStop-1] osweb.context.ContextLoader : 루트 WebApplicationContext : 1358ms에 초기화 완료
2019-03-05 10 : 57 : 51.698 정보 45469 --- [ost-startStop-1] osbceServletRegistrationBean : 서블릿 매핑 : 'dispatcherServlet'을 [/]로
2019-03-05 10 : 57 : 51.702 정보 45469 --- [ost-startStop-1] osbcembedded.FilterRegistrationBean : 매핑 필터 : 'hiddenHttpMethodFilter'to : [/ *]
```

1. 날짜 시간
2. 로그 레벨
3. 프로세스 ID
4. `---` 실제 로그 메시지 구분 기호
5. 스레드 이름
6. 로거 이름
7. 로그 메시지

### 파일 설정

- logging.file.path / logging.file.name 속성값을 이용해 파일로 로그를 저장할 수 있습니다.

![image](https://user-images.githubusercontent.com/53366407/118116636-771b4e80-b425-11eb-896b-24650f70ac24.png)

### 로그 레벨 설정

```jsx
logging.level.root=warn
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
```

- 위와 같이 각 모듈들을 다르게 로그 레벨을 설정 할 수 있습니다.

### Custom Log

![image](https://user-images.githubusercontent.com/53366407/118116573-67036f00-b425-11eb-9870-88c5aa3cd2c9.png)

### 추가적으로 설명 할 것

[Spring Logback Profile 조합 전략](https://rutgo-letsgo.tistory.com/118?category=788360)

[Spring에서으로 Log를 Slack으로 받아보기](https://rutgo-letsgo.tistory.com/125?category=788360)

## JSON

- Gson
    - Gson 모듈이 있다면 자동으로 구성됩니다.
- Jackson (기본 라이브러리)
    - 기본적으로 ObjectMapper가 빈으로 구성되어 처리합니다.
- JSON-B
    - Jsonb 모듈이 있으면 자동으로 구성됩니다.

## Developing Web Application

### Spring Web MVC

```jsx
@RestController
@RequestMapping(value="/users")
public class MyRestController {

    @RequestMapping(value="/{user}", method=RequestMethod.GET)
    public User getUser(@PathVariable Long user) {
        // ...
    }

    @RequestMapping(value="/{user}/customers", method=RequestMethod.GET)
    List<Customer> getUserCustomers(@PathVariable Long user) {
        // ...
    }

    @RequestMapping(value="/{user}", method=RequestMethod.DELETE)
    public User deleteUser(@PathVariable Long user) {
        // ...
    }

}
```

- MVC 프레임워크로 HTTP 요청을 처리하고 응답하는 방법입니다.

### Spring MVC Auto-configuration

1. WebJar에 대한 지원
2. ContentNegotiatingViewResolver
3. BeanNameViewResolver
4. 정적 리소스 제공
5. Converter, GenericConverter, Forammter
6. HttpMessageConverters
    - HTTP 요청과 응답을 변환하며, 기본적으로 UTF-8을 사용합니다.
7. MessageCodesResolver
    - 오류 메시지를 랜더링 하는 것
8. idnex.html 및 정적 컨텐츠
    - static, public, resource 가 기본 디렉토리를 사용합니다.
    - `spring.mvc.static-path-pattern` 를 사용해 조정할 수 있습니다.
9. ConfigurableWebBindingInitializer
    - 특정 요청을 초기화 한다.

### Template Engines

- [FreeMarker](https://freemarker.apache.org/docs/)
- [Groovy](https://docs.groovy-lang.org/docs/next/html/documentation/template-engines.html#_the_markuptemplateengine)
- [Thymeleaf](https://www.thymeleaf.org/)
- [Mustache](https://mustache.github.io/)

### Exception

```jsx
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {

    @ExceptionHandler(YourException.class)
    @ResponseBody
    ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
        HttpStatus status = getStatus(request);
        return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
    }

    private HttpStatus getStatus(HttpServletRequest request) {
        Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
        if (statusCode == null) {
            return HttpStatus.INTERNAL_SERVER_ERROR;
        }
        return HttpStatus.valueOf(statusCode);
    }

}
```

- 위 코드에서 ControllerAdvice와 ExceptionHandler를 이용해 특정 예외 유형에 대해 반환할 수 있습니다.
- `/error` 디렉토리에서 상태 코드.html를 만들면 오류 페이지를 설정 할 수 있습니다.

### CORS

```jsx
@Configuration(proxyBeanMethods = false)
public class MyConfiguration {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**");
            }
        };
    }
}
```

[CORS에 대해서 알아보자.](https://rutgo-letsgo.tistory.com/151)

## Embedded Servlet Container Support

1. Tomcat
2. Jetty
3. Undertow

### 설정값

- 네트워크 설정 : 들어오는 HTTP 요청 ( `server.port`)에 대한 수신 대기 포트 , 바인딩 할 인터페이스 주소 등 `server.address`.
- 세션 설정 : 세션이 지속되는지 ( `server.servlet.session.persistent`), 세션 시간 초과 ( `server.servlet.session.timeout`), 세션 데이터 위치 ( `server.servlet.session.store-dir`) 및 세션 쿠키 구성 ( `server.servlet.session.cookie.*`).
- 오류 관리 : 오류 페이지 ( `server.error.path`) 의 위치 등입니다 .
- [SSL](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/howto.html#howto-configure-ssl)
- [HTTP 압축](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/howto.html#how-to-enable-http-response-compression)

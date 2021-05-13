# Spring Boot Features - 2

[Spring Boot Features](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-profiles)

<br>

## Profile

Spring Profile은 Application configuration을 분리하고 특정한 환경에서만 사용가능하도록 하는 기능을 제공하고 있다.

`@Component`, `@Configuration`, `@ConfigurationProperties` 를 사용하는 경우, `@Profile` 과 함께 사용하면 load 시에 명시한 Profile로 제한한다.

```java
@Configuration(proxyBeanMethods = false)
@Profile("production")
public class ProductionConfiguration {
    // ...
}
```
<br>


`spring.profile.active`  Environment property를 통해 active 할 profile을 지정할 수 있다.

- `spring.profiles.active=dev,hsqldb` at `application.properties`
- `--spring.profiles.active=dev,hsqldb` at Command line

<br>

만약 둘 다 주어진다면, `application.properties` 에 정의한 profile은 command line에 정의된 profile에 의해 대체될 수 있다.

- 때때로 대체하는 것보다 추가적으로 profile을 구성하는 것이 필요할 때가 있는데 이럴 땐
    1. `[SpringApplication` 클래스의 `setAdditionalProfiles()`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringApplication.html#setAdditionalProfiles-java.lang.String...-) 을 사용
        - Application run 전에 `SpringApplication.setAdditionalProfiles(…)` 을 호출하면 Profile을 코드로서 선언할 수 있다.
    2.  여러 Profile을 사용할 수 있도록 지원하는 Profile groups를 사용

        ```java
        // --spring.profiles.active=production 을 통해 activation
        spring.profiles.group.production[0]=proddb
        spring.profiles.group.production[1]=prodmq
        ```

<br>


## Logging

Spring Boot는 모든 내부 Logging에 있어 Commons Logging를 사용한다. 

- Log interface로 Logging 추상화 계층을 제공한다. 이를 통해 개발자들은 Logging 구현체가 무엇이든 (Commons Logging interface에서 제공하는) 동일한 방법으로 쉽게 사용할 수 있다.

<br>

기본 logging configuration으로는 `Java Util Logging`, `Log4j2`, `Logback` 이 제공되며, 이러한 logger들은 log output으로 **console output을 사용하도록 기본값(default)으로 설정**되어있고, 만약 원할 시 file output으로도 설정이 가능하다.

만약 "Starters"를 사용한다면, 로깅 시에 **기본값(default)으로 `Logback` 을 사용**한다. 만약 다른 Logger를 사용하고 싶다면, 의존성을 추가해 사용할 수 있다.

<br>

### Log format

```bash
2019-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2019-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2019-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
```

Log output은 공백으로 구분된 일정한 순서를 가진다.

1. **Date and Time**
2. **Log level**: `ERROR`> `WARN`> `INFO`> `DEBUG`> `TRACE`
    - `FATAL` 은? → `Logback` 은 `FATAL` 을 지원하지 않는다. `ERROR` 로 매핑된다.
3. **Process ID (PID)**
4. **---** : 구분자
5. **[ Thread name ]**
6. **Logger name**: 보통 source class name → Log 생성 시, class name 넣어주는 이유
7. **Log message**

<br>

### Console output

기본값(default)으로 Log는 `INFO` 레벨 이상(`ERROR`, `WARN`, `INFO`)의 Log들만 출력된다.

```bash
$ java -jar myapp.jar --debug
```

`--debug` 모드를 통해 application을 실행시키면, Core loggers(embedded container, Hibernate, Spring Boot)가 더 많은 정보를 출력하도록 설정이 된다.

- 이 뜻이 모든 Log 메시지를 `DEBUG` 레벨로 변경하겠다는 뜻이 아니므로 주의하자.
- 만약 Log 메시지 출력 레벨을 정하고 싶다면 `application.properties` 에서 `logging.level.package=DEBUG` 식으로 정할 수 있다.

<br>

### File Output

앞서 말했듯, 기본값(default)으로는 console에 출력할 뿐, log files를 남기지 않는다.

- 만약 log files를 작성하고 싶다면, `logging.file.name`  또는 `logging.file.path`  속성을 통해 설정할 수 있다. (`application.properties`)
    ![Untitled](https://user-images.githubusercontent.com/37873745/118115350-c95b7000-b423-11eb-905b-0e6d85cdf110.png)
- `application.properties` 에서는 name, path 둘 다 함께 작성하면 `logging.file.name`  만 적용이 된다. 만약 특정 경로에 특정 이름으로 log 파일을 만들고 싶은 경우엔 name 속성으로 디렉토리까지 명시하면 된다.
    - ex) `logging.file.name=/var/log/some_application.log`

<br>

### File Rotation

Log rotation은 Log 파일 작성시 꼭 필요한 존재이다. 아무런 설정이 없다면, 10MB에 도달시 자동으로 rotate한다.

Logback을 사용한다면 `application.properties` 를 통해 여러 설정을 할 수 있도록 지원하고 있다.

![Untitled 1](https://user-images.githubusercontent.com/37873745/118115334-c6607f80-b423-11eb-88ff-b2daa99f7d50.png)

<br>

### Log Groups

특정 그룹마다 Logger 레벨을 달리 설정하고 싶을 수 있다. 그런 경우, Log group을 형성하고 다른 로깅 레벨을 설정하면 된다.

예를 들어보자.

```yaml
logging:
  group:
    tomcat: "org.apache.catalina,org.apache.coyote,org.apache.tomcat"
```

```yaml
logging:
  level:
    tomcat: "trace"
```

- 이런 경우, group에서 명시한 패키지의 Logger 레벨들은 trace로 설정되게 된다.

이런 Group은 `web` , `sql` 사용시 유용하게 사용되어서 Spring Boot에서 자체적으로 제공하는 log group이 있다.

![Untitled 2](https://user-images.githubusercontent.com/37873745/118115341-c791ac80-b423-11eb-8092-5646b7d06899.png)
![Untitled 3](https://user-images.githubusercontent.com/37873745/118115346-c8c2d980-b423-11eb-8af4-b55be4214dc9.png)

- 예를 들어보자. web application을 개발 중인데, web 개발 관련 패키지에서만 로그들이 debug로 출력되었으면 좋겠다. 그런 경우, 아래처럼 적용하면 된다.

```yaml
logging.level.web=DEBUG
```

<br>

### Custom Log Configuration

Spring boot에서는 `org.springframework.boot.logging.LoggingSystem` system property를 사용함으로서 특정한 logging system을 사용하도록 강제할 수 있다.

`logging.config` 설정 

[Log4j 2 제대로 사용하기 - 설정](https://velog.io/@bread_dd/Log4j-2-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

`logback` 설정 파일

[[스프링부트 (5)] Spring Boot 로그 설정(1) - Logback](https://goddaehee.tistory.com/206)
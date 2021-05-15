# 5. Spring Boot Features (2 / 3)

## 4. Logging

Spring Boot uses Commons Logging for all internal logging but leaves the underlying log implementation open. Default configurations are provided for Java Util Logging, Log4J2, and Logback. In each case, loggers are pre-configured to use console output with optional file output also available.

By default, if you use the “Starters”, Logback is used for logging. Appropriate Logback routing is also included to ensure that dependent libraries that use Java Util Logging, Commons Logging, Log4J, or SLF4J all work correctly.

### 4.1. Log Format

```shell
2019-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2019-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2019-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
```

* Date and Time: Millisecond precision and easily sortable.
* Log Level: ERROR, WARN, INFO, DEBUG, or TRACE.
* Process ID.
* A --- separator to distinguish the start of actual log messages.
* Thread name: Enclosed in square brackets (may be truncated for console output).
* Logger name: This is usually the source class name (often abbreviated).
* The log message.

### 4.2. Console Output

디버그 레벨 로그
실행시, --debug나, -Ddebug 명령을 주면, 디버그레벨의 로그도 찍힌다.(embedded container, Hibernate, Spring Boot 만)

![debug-example](https://media.vlpt.us/post-images/max9106/57acbd60-48f4-11ea-bece-ad3059b279e1/-2020-02-07-12.21.12.png)

### 4.3. File Output

기본적으로 Spring Boot는 콘솔에만 로깅하고 로그 파일을 작성하지 않습니다.  
콘솔 출력과 함께 로그 파일을 작성하려면 logging.file.name 또는 logging.file.path 속성을 설정해야합니다 (예 : application.properties).

```shell
logging.file.name=my.log
logging.file.path=/var/log
```

### 4.4. File Rotation

Logback을 사용하는 경우 application.properties 또는 application.yaml 파일을 사용하여 Log Rotate 설정을 미세 조정할 수 있습니다. 다른 모든 로깅 시스템의 경우 직접 Rotate 설정을 구성해야합니다 (예 : Log4J2를 사용하는 경우 log4j.xml 파일을 추가 할 수 있음).

|이름|샘플 값|설명|
|---|---|---|
|logging.logback.rollingpolicy.file-name-pattern|${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz|로그 아카이브를 만드는 데 사용되는 파일 이름 패턴|
|logging.logback.rollingpolicy.clean-history-on-start|false|애플리케이션이 시작될 때 로그 아카이브 정리가 발생해야하는 경우|
|logging.logback.rollingpolicy.max-file-size|10MB|아카이브되기 전 로그 파일의 최대 크기|
|logging.logback.rollingpolicy.total-size-cap|10MB|로그 아카이브를 삭제할 수있는 최대 크기|
|logging.logback.rollingpolicy.max-history|7|로그 아카이브 보관 일수 (기본값 : 7)|

다른 옵션들 : https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/appendix-application-properties.html#common-application-properties.core

### 4.5. Log Levels

1. ERROR	사용자 요청을 처리하는 중 문제가 발생함.
2. WARN	처리 가능한 문제지만, 향후 시스템 에러의 원인이 될 수 있음.
3. INFO	로그인이나 상태 변경과 같은 정보성 메시지
4. DEBUG	개발 시 디버깅 목적으로 출력하는 메시지
5. TRACE	DEBUG레벨보다 좀 더 상세한 메시지

로그 레벨은 우선 순위가 정해져 있어서 특정 로그 레벨을 지정하면 상위 우선순위 로그가 모두 출력된다.  
예를 들어 DEBUG로 지정하면 DDEBUG를 시작으로 INFO,WARN,ERROR 메시지가 모두 출력된다.  
만약 WARN으로 지정하면 WARN,ERROR 에 해당하는 로그 메시지만 출력된다.

따라서 개발단계에서는 DEBUG나 TRACE로 설정하지만 실제 운영 단계로 넘어가면 WARN 이상의 레벨로 조정해야 한다.

### 4.6. Log Groups

패키지 별로 로그 레벨을 설정할 수 있다.  
최상위 패키지의 위치는 너무 길어서 외우기 어렵다.  
복잡한 패키지는 로그 그룹으로 만들어서 관리할 수 있다.  

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

스프링부트가 미리 정의해놓은 로그 그룹들도 있다.

|이름|로거|
|---|---|
|web|`org.springframework.core.codec`, `org.springframework.http`, `org.springframework.web`, `org.springframework.boot.actuate.endpoint.web`, `org.springframework.boot.web.servlet.ServletContextInitializerBeans`|
|sql|`org.springframework.jdbc.core`, `org.hibernate.SQL`, `org.jooq.tools.LoggerListener`|


### 4.7. Using a Log Shutdown Hook

로깅 리소스를 해제하려면 일반적으로 애플리케이션이 종료 될 때 로깅시스템을 중지하는 것이 좋다.  
모든 애플리케이션 유형에서 작동하는 단일 방법은 없다.
애플리케이션에 복잡한 컨텍스트 계층이 있거나 war 파일로 배포 된 경우 기본 로깅 시스템에서 직접 제공하는 옵션을 살펴봐야 한다.
Logback은 각 로거를 자체 컨텍스트에서 생성 할 수있는 context selectors를 제공한다.

자체 JVM에 배포 된 간단한 "단일 jar"를 사용한다면, `logging.register-shutdown-hook` 프로퍼티를 사용할 수 있다.  
이걸 true로 설정하면 JVM이 종료될 때 로그 시스템을 정리해주는 shutdown hook이 등록될 것이다.

```yaml
logging:
  register-shutdown-hook: true
```

### 4.8. Custom Log Configuration

다양한 로깅 시스템은 클래스 경로에 적절한 라이브러리를 포함하여 활성화 할 수 있으며 클래스 경로의 루트 또는 다음 Spring 환경 속성에 지정된 위치 (loging.config)에 적절한 구성 파일을 제공하여 추가로 사용자 정의 할 수 있다.

|Logging System|Customization|
|---|---|
|Logback|`logback-spring.xml`, `logback-spring.groovy, logback.xml`, or `logback.groovy`|
|Log4j2|`log4j2-spring.xml` or `log4j2.xml`|
|JDK (Java Util Logging)|`logging.properties`|

### 4.9. Logback Extensions

참고자료

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-logging  
https://www.baeldung.com/spring-boot-logging  
https://velog.io/@max9106/Spring-Boot-%EB%A1%9C%EA%B9%85-3nk6avy3vm  
https://dololak.tistory.com/635


# 5. Spring Boot Features (2 / 3)

## 4. Logging

### 4.1. Log Format

```aidl
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



### 4.3. File Output

### 4.4. File Rotation

### 4.5. Log Levels

### 4.6. Log Groups

### 4.7. Using a Log Shutdown Hook

### 4.8. Custom Log Configuration

### 4.9. Logback Extensions

참고자료

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-logging
https://velog.io/@ehdrms2034/%EC%8A%A4%ED%94%84%EB%A7%81-Boot-%EB%A1%9C%EA%B9%85-Logging
https://velog.io/@max9106/Spring-Boot-%EB%A1%9C%EA%B9%85-3nk6avy3vm

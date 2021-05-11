# 6week. spring-boot-features 3,4,5,6

- https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-profiles

## 3. Profiles

프로파일 설정을 통해 환경에 따른 환경 변수 설정이 가능

```yml
spring:
  profiles:
    active: "dev,hsqldb"


2.4부터 사용 가능 위의 부분 deprecate되었지만 사용 가능
spring:
  config:
    activate:
      on-profile: dev
```

> 실제 사용 법 : 프로파일이 local 또는 beta일 경우에 six를 빈으로 등록

```java
@Configuration
@Slf4j
@Profile({"local", "beta"})
public class SixConfig {

    @Bean
    public String six(){

        log.info("say!!! profile working");

        return "six";
    }
}
```

@ProfileCondition 어노테이션에서 확인, 디버깅 잠깐 해보는 것도 나쁘지 않다고 판단!
```java
class ProfileCondition implements Condition {

	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		MultiValueMap<String, Object> attrs = metadata.getAllAnnotationAttributes(Profile.class.getName());
		if (attrs != null) {
			for (Object value : attrs.get("value")) {
				if (context.getEnvironment().acceptsProfiles(Profiles.of((String[]) value))) {
					return true;
				}
			}
			return false;
		}
		return true;
	}

}
```


아래와 같이 그룹으로 사용 가능
```yml
spring:
  profiles:
    group:
      production:
      - "proddb"
      - "prodmq"
```


## 4. Logging

| Name   |      Description      | 
|----------|:-------------:|
| logging.logback.rollingpolicy.file-name-pattern |  The filename pattern used to create log archives. | 
|logging.logback.rollingpolicy.max-file-size|    The maximum size of log file before it’s archived.   |   
|logging.logback.rollingpolicy.max-history | The number of days to keep log archives (defaults to 7) |   


> logback-spring.xml 을 통해 콘솔로깅, 파일 로깅 등등 설정 가능

## 6. JSON

- Gson
- Jackson
- JSON-B : postgrSQL에서 쓰이는 것으로 추정


## 7. Developing Web Applications

예시를 만들려 하였으나 지금은 더이상 발생하지 않는 문제
path variable에 .이 포함되었을 경우
-  https://www.baeldung.com/spring-mvc-pathvariable-dot


### 7.1 The “Spring Web MVC Framework”

> MessageConvertor를 활용하여 timeformat을 맞춰보자

profile이 local일떄는? 응답이? 아닐때는?

```java
@Configuration
@Profile("local")
public class SixWebConfig extends WebMvcConfigurationSupport {

  @Override
  protected void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    converters.add(customMessageConverter());
  }

  private HttpMessageConverter<?> customMessageConverter() {

    MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter();
    converter.setObjectMapper(customMapper());
    return converter;
  }

  private ObjectMapper customMapper() {
    return CustomObjectMapper.setting();
  }
}

public class CustomObjectMapper {

    public static ObjectMapper setting(){
        ObjectMapper objectMapper = new ObjectMapper();

        JavaTimeModule javaTimeModule = new JavaTimeModule();
        javaTimeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ISO_LOCAL_DATE_TIME));
        javaTimeModule.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ISO_LOCAL_DATE_TIME));
        objectMapper.registerModule(javaTimeModule);

        return objectMapper;
    }
}

```

- profile local then : {"now":"2021-05-11T23:47:48.485"}
- profile is not local then : {"now":[2021,5,11,23,50,32,487000000]}

위처럼 시간과 관련된 부분을 클라와 협의하여 진행!! 타임스탬프를쓸것인지 등등에 대한 결정이 필요

좋아하는 에러 핸들링

```java
@RestControllerAdvice
@Slf4j
public class SampleAdviceController {

  @ExceptionHandler(SampleException.class)
  public ResponseEntity<ClientErrorResponse> handleSampleException(SampleException e) {

    ClientErrorResponse clientErrorResponse = new ClientErrorResponse(e.getErrReason());
    return new ResponseEntity<>(clientErrorResponse, HttpStatus.valueOf(e.getHttpStatusCode()));
  }
}

@Getter
public class SampleException extends RuntimeException {

  public SampleException(Integer httpStatusCode, String errReason) {
    this.httpStatusCode = httpStatusCode;
    this.errReason = errReason;
  }

  private Integer httpStatusCode;
  private String errReason;
}

public class ForbiddenException extends SampleException{
    public ForbiddenException(String errReason) {
        super(403, errReason);
    }
}

@Getter
public class ClientErrorResponse {

    private String reason;

    public ClientErrorResponse(String reason) {
        this.reason = reason;
    }
}

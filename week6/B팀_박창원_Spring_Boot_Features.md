# 웹 앱 개발하기, Developing Web Application

스프링부트는 웹 애플리케이션 개발에 적합하다.

내장형 톰캣, 제티, 언더토우, 네티를 이용하여 HTTP 서버를 제작할 수 있다.

대부분의 웹 애플리케이션은 `spring-boot-starter-web` 모듈을 사용하여 빠르게 실행할 수 있다.

## 스프링 웹 MVC 프레임워크

스프링 MVC는 **"model view controller"** 웹 프레임워크다. 

스프링 MVC는 @Controller 혹은 @RestController 빈을 생성하여 요청받는 HTTP를 다룰 수 있도록 한다.

## 스프링 MVC 자동설정

스프링 부트는 스프링 MVC를 위한 자동 설정을 제공한다.

자동 설정은 아래의 기능을 스프링의 기본 설정에 덧붙인다.

- `ContentNegotiatingViewResolver` 빈과 `BeanNameViewResolver` 빈
- 정적 리소스를 제공을 위한 지원, WebJars 지원
- Converter, GenericConverter, Fromatter 빈들이 자동 등록된다.
- HttpMessageConvereters 지원
- MessageCodesResolver 자동 등록
- 정적 index.html 지원
- ConfigurationWebIndingInitializer 빈의 자동 사용

만약 인터셉터, 포매터, 뷰 컨트롤러 등의 기능을 커스터마이제이션하려면, `@Configuration` 클래스를 생성하여 `@EnableWebMvc` 없이`WebMvcConfigurer` 타입을 상속하도록 하라.



WebMvcConfigurer는 인터페이스 타입이다. 모든 메서드가 default로 되어 있으므로, 선택적으로 오버라이드할 수 있다.

WevMvcConfigurer를 통해 다음의 설정이 가능하다.

- URL Path 매칭 설정
- Content Negoitation(자원의 여러 포맷중 어떤 포맷을 내보낼 것인가에 대함)
- 기본 서블릿핸들링(핸들러가 기본 서블릿 컨테이너에 의해 포워딩된 다뤄지지 않은 요청을 위임함)
- 컨버터와 포매터 추가
- 인터셉터 추가 (컨트롤러 호출의 이전과 이후에 추가할 동작)
- 리소스 핸들러 추가
- cross origin request 설정
- 뷰 컨트롤러 추가(404 페이지, 메인 페이지등을 추가할 때 유용함 )
- 뷰 리졸버 추가(뷰 리졸버가 문자열 기반의 뷰 이름을 번역하여 `org.springframework.web.servlet.View` 객체로 변환함 )
- 아규먼트 리졸버(내장된 리졸빙 핸들러 메소드 아규먼트를 오버라이드할 수 없다)
- 리턴 밸류 핸들러
- **메시지 컨버터 설정 및 확장**( 요청과 응답 본문 메시지를 읽거나 쓸 때 사용된다.)
- 예외 리졸버 설정 및 확장
- 밸리데이터(Validator) 설정
- 메시지 코드 리졸버(데이터를 바인딩하거나 밸리데이션 에러 발생시 생성되는 메시지 코드를 만들어줌)



만약 `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, `ExceptionHandlerExceptionResolver`의 커스텀 인스턴스를 만들고 싶으면서 스프링 부트 MVC의 커스터마이제이션을 유지하고 싶다면, `WebMvcRegistrations` 빈 타입을 선언하면 된다.



만약 스프링 MVC을 완전하게 제어하고 싶다면, @EnableWebMvc를 붙인 @Configuration 클래스를 추가하라. 또는 @Configuration 클래스에 DelegatingWebMvcConfiguration을 추가하라



## HttpMessageConverters

스프링 MVC는 HttpMessageConvereter 인터페이스를 사용하여 HTTP 요청과 응답을 변환한다.

Jackson Library를 사용한다면, 객체가 JSON 또는 XML로 자동으로 변환될 수 있다.

만약 커스텀 컨버터를 추가해야한다면, 스프링 부트의 `HttpMessageConverter` 클래스를 사용하라.

```java
import org.springframework.boot.autoconfigure.http.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;

@Configuration(proxyBeanMethods = false)
public class MyConfiguration {

    @Bean
    public HttpMessageConverters customConverters() {
        HttpMessageConverter<?> additional = ...
        HttpMessageConverter<?> another = ...
        return new HttpMessageConverters(additional, another);
    }

}
```



## 커스텀 JSON Serializers/Deserializers 

만약 Jackson 라이브러리를 사용하여 JSON 데이터를 직렬화/역직렬화한다면, `JsonSerializer` 와 `JsonDeserializer` 를 커스텀하여 작성하라.

커스텀 직렬화기는 보통 모듈을 통해 등록된다.([registered with Jackson through a module](https://github.com/FasterXML/jackson-docs/wiki/JacksonHowToCustomSerializers)) 

반면, 스프링부트는 `@JsonComponent` 어노테이션을 사용하여 쉽게 스프링 빈으로 등록하게 한다.

`@JsonComponent` 를 사용하여 아래와 같이 어떤 객체(여기서는 `SomeObject`) 의 직렬화기/역직렬화기를 생성할 수 있다.

```java
import java.io.*;
import com.fasterxml.jackson.core.*;
import com.fasterxml.jackson.databind.*;
import org.springframework.boot.jackson.*;

@JsonComponent
public class Example {

    public static class Serializer extends JsonSerializer<SomeObject> {
        // ...
    }

    public static class Deserializer extends JsonDeserializer<SomeObject> {
        // ...
    }

}
```

스프링 부트가 제공하는  [`JsonObjectSerializer`](https://github.com/spring-projects/spring-boot/tree/v2.4.3/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/jackson/JsonObjectSerializer.java) and [`JsonObjectDeserializer`](https://github.com/spring-projects/spring-boot/tree/v2.4.3/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/jackson/JsonObjectDeserializer.java) 베이스 클래스는 객체를 직렬화할 때, 표준 Jackson 버전을 활용할 수 있다.



## HttpMessageConveters 실습



1. 도메인 객체 정의

먼저, 변환할 객체를 정의한다.

```java
public class MeetCoder {
	private String name;
	private int age;
}
```

2. 커스텀 HttpMessageConverter 정의

HttpMessageConverter 를 상속해야 하는데, AbstractHttpMessageConvereter로 간단하게 정의할 수 있다.

```java

public class MeetCoderHttpMessageConverter extends AbstractHttpMessageConverter<MeetCoder> {

	public MeetCoderHttpMessageConverter() {
		super(new MediaType("text", "plain"));
	}

	@Override
	protected boolean supports(Class<?> clazz) {
		return MeetCoder.class.isAssignableFrom(clazz);
	}

	@Override
	protected MeetCoder readInternal(Class<? extends MeetCoder> clazz, HttpInputMessage inputMessage) throws HttpMessageNotReadableException, IOException {
		String requestBody = toString(inputMessage.getBody());
		int i = requestBody.indexOf("\n");
		if (i == -1) {
			throw new HttpMessageNotReadableException("No first line found");
		}

		String name = requestBody.substring(0, i).trim();
		int age = Integer.parseInt(requestBody.substring(i).trim());

		return new MeetCoder(name, age);
	}

	@Override
	protected void writeInternal(MeetCoder meetCoder, HttpOutputMessage outputMessage) throws HttpMessageNotWritableException {
		try {
			OutputStream outputStream = outputMessage.getBody();
			String body = meetCoder.getName() + "\n" + meetCoder.getAge();
			outputStream.write(body.getBytes());
			outputStream.close();
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	private static String toString(InputStream inputStream) {
		Scanner scanner = new Scanner(inputStream, "UTF-8");
		return scanner.useDelimiter("\\A").next();
	}
}

```



3. WebMvcConfigurer의 `extendMessageConvereters()` 메소드를 오버라이드하여 커스텀 컨버터를 추가한다.

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
	@Override
	public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
		converters.add(new MeetCoderHttpMessageConverter());
	}
}
```



4. 마지막으로 컨트롤러를 등록하여 확인한다.

위에서 지정한 MediaType인 `text/plain` 으로 정의하라.

```java
@Slf4j
@RestController
public class MeetCoderController {
	@GetMapping(value = "/meetcoders", consumes = "text/plain")
	public MeetCoder get(@RequestBody MeetCoder meetCoder) {
		log.info("{}", meetCoder);
		return meetCoder;
	}
}
```



테스트를 하기위해 /meetcoders로 요청하자.

Content-Type이 text/plain이여야 한다는 것을 잊지말자.

메시지 본문은 아래와 같이 readInternal()에서 정의한 로직에 맞춰야 파싱할 수 있다.

```text
test
123
```




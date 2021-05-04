# SpringApplication

Let’s dive into the details of Spring Boot !

- `SpringApplication` 클래스를 이용해서 main() 메소드 안에서 스프링 어플리케이션을 구동시킨다.
- 많은 상황에서 `SpringApplication.run()` 이라는 static method 를 사용할 수 있다.

```java
public static void main(String[] args) {
  SpringApplication.run(MySpringConfiguration.class, args);
}
```

```kotlin
fun. main(args: Array<String>) {
  runApplication<MySpringConfiguration>(*args)
}
```

어플리케이션이 시작하면 다음과 비슷한 output 을 볼 수 있다.

```
.   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::   v2.4.3

2019-04-31 13:09:54.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
2019-04-31 13:09:54.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
2019-04-01 13:09:56.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
2019-04-01 13:09:57.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)
```

- 기본값으로, startup 과 관련된 디테일한 정보를 포함해서 INFO 로그 메세지가 보여진다.
- 프로퍼티 값을 설정하면 INFO 레벨이 아닌 로그도 확인할 수 있다.
- Startup information logging 이 필요 없는 경우 spring.main.log-startup-info 를 false 로 설정하면 된다. 어플리케이션의 active profile 의 로깅 또한 되지 않을 것이다.
- Startup 단계에서 추가적인 로깅정보가 필요하면 SpringApplication 를 상속받은 클래스에서 `logStartupInfo(boolean)` 메소드를 오버라이딩 하면 된다.

## 1. Startup Failure

- 어플리케이션에 구동에 실패하면 `FailureAnalyzers` 가 에러 메세지와 구체적인 해결법을 제공할 기회를 갖는다.
- 예를들어 8080 포트에 웹 어플리케이션을 띄우려는데 이미 포트가 사용중인 경우, 아래와 같은 메세지를 보게 될 것이다.
- 스프링 부트는 여러 `FailureAnalyzer` 구현체를 제공하며, 필요한 경우 우리가 직접 만들어 추가할 수 있다.

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```

- 만약 발생한 예외를 핸들링할 수 있는 FailureAnalyzer가 없는 경우, 모든 condition report 를 출력하여 문제점을 파악하는데 도움을 받을 수 있다. 이 경우 debug 프로퍼티를 활성화 하고 debug logging 을 활성화하면 된다.
예를 들어 java -jar 로 어플리케이션을 실행하는 경우에는 debug 프로퍼티를 아래 명령어로 활성화 할 수 있다.

```bash
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

## 2. Lazy Initialization

- SpringApplication 을 이용해서 lazy initialization 을 할 수 있다.
- Lazy initialization을 사용하면, 빈들이 application 구동시에 생성되지 않고, 필요한 시점에 만들어지게 된다.
- 그 결과, 더 짧은 시간에 어플리케이션이 구동된다.

- 예를 들어, 웹 어플리케이션의 경우, lazy initialization 을 활성화하면, 웹과 관련된 빈들은 HTTP request 를 받을 때가 되서야 초기화 될 것이다.

- 단점으로는, 어플리케이션에 문제점이 있을 때 추후에 발견될 수 있다. 잘못 설정된 빈이 lazy initialized 되는 경우, failure 가 어플리케이션 구동시점이 아니라 해당 빈이 초기화 되는 시점에 드러나게 될 것이다.
- 또한 JVM 이 충분한 메모리를 사용하고 있는지 체크해야 한다. 구동시에는 메모리가 충분하더라도 아직 어플리케이션에 사용되는 빈들이 다 만들어지지 않았기 때문에!

- 이러한 이유 때문에, lazy initialization 을 사용하지 않는 것이 기본값이고 lazy initialization 을 사용하려면 JVM 의 힙 사이즈를 적당히 조절해야 한다.

- Lazy initialization 활성화 방법
    1. SpringApplicationBuilder 의 lazyInitialization()
    2. SpringApplication 의 setLazyInitialization()
    3. spring.main-lazy-initialization = true

- @Lazy(false) 어노테이션을 이용해서 나머지 빈에 대해서는 lazy initialization 을 활성화 하고 특정 빈에 대해서만 lazy initialization 을 비활성화 할 수 있다.

## 3. Customizing the Banner

- Startup 시 출력되는 배너를 설정할 수 있다.
- 텍스트 배너 위에 이미지 배너를 띄울 수 있다. 이미지 배너는 ASCII art 로 변환되어 출력된다.
- 출력되는 배너는 springBootBanner 라는 이름의 싱글톤 빈으로 등록된다.
- 관련 프로퍼티

```yaml
spring:
  banner:
    location: {file-path} # 혹은 banner.txt 이름의 파일을 classpath 에 추가
    charset: {encoding}
    image:
      location: {file-path} # 혹은 banner.gif, banner.jpg, banner.png 이름의 파일을 classpath 에 추가
  main:
    banner-mode: {mode} # System.out (console), logger (log), not produced at all (off)
```

- Banner.txt 파일 안에서 아래 변수들을 사용할 수 있다.

![255DD053-9AC5-4DC5-9FAD-DEDA19BECD31](https://user-images.githubusercontent.com/40685291/116958225-cef2e080-acd4-11eb-87e0-022ea0c1db6c.jpeg)

- SpringApplication.setBanner(...) 메소드를 이용해서 배너를 코드상 만들어 사용할 수 있다.

```kotlin
fun main(args: Array<String>) {
    val app: SpringApplication = SpringApplication(MySpringConfiguration::class.java)
    app.setBanner(object: Banner {
        override fun printBanner(environment: Environment?, sourceClass: Class<*>?, out: PrintStream?) {
            TODO("Not yet implemented")
        }
    })
    app.run(*args)
}
```

## 4. Customizing SpringApplication

SpringApplication 의 기본값이 마음에 들지 않는 경우에 인스턴스를 만들어 커스터마이징 할 수 있다. 예를 들어 배너를 사용하고 싶지 않은 경우는 아래와 같이 코드를 수정할 수 있다.

```java
public static void main(String[] args) {
  SpringApplication app = new SpringApplication(MySpringConfiguration.class);
  app.setBannerMode(Banner.Mode.OFF);
  app.run(args);
}
```

또는 application.properties 파일을 수정해서 SpringApplication 을 설정 값들을 변경할 수 있다.

## 5. Fluent Builder API

만약 ApplicationContext 의 계층구조(부모 관계를 맺은 여러개의 context)를 사용하거나, 빌더 패턴을 사용하고 싶으면 SpringApplicationBuilder 를 사용할 수 있다.

```java
new SpringApplicationBuilder()
			.sources(Parent.class)
			.child(Application.class)
			.bannerMode(Banner.Mode.OFF)
			.run(args);
```

[ApplicationContext hierarchy](## ApplicationContext?)

ApplicationContext 계층구조를 사용하는 경우 몇 제약 사항이 있다. 
예를들어 웹 컴포넌트들은 자식 컨텍스트에 반.드.시. 포함되어 있어야 하고 부모와 자식 컨텍스트에서 똑같은 `Environment` 가 사용되어야 한다

## 6. Application Availability

- 어떤 플랫폼에 배포될 때 어플리케이션은 플랫폼에 Kubernates Probes 와 같은 인프라 구조를 이용해서 availability 정보를 제공할 수 있다.
- 스프링 부트는 liveness 와 readiness 라는 availability state 를 제공한다
- 만약 스프링 부트의 actuator 를 사용하고 있다면 이러한 state 정보들은 health endpoint group 의 형태로 전달된다.
- 또는 빈에 `ApplicationAvailability` 라는 인터페이스를 주입함으로써 availability state 데이터를 얻을 수 있다.

### 6.1. Liveness State

- Liveness 상태는 애플리케이션이 내부적으로 잘 작동하고 있거나 문제가 있을 때 (failing) 스스로 문제를 해결할 수 있는지를 나타내는 상태값이다
- broken 상태는 애플리케이션이 회복되지 못하기에 애플리케이션을 재시작하는게 더 낫다는 의미이다

```java
public enum LivenessState implements AvailabilityState {

	/**
	 * The application is running and its internal state is correct.
	 */
	CORRECT,

	/**
	 * The application is running but its internal state is broken.
	 */
	BROKEN

}
```

- An application is considered live when it's running with a correct internal state.
- "Liveness" failure means that the internal state of the application is broken and we cannot recover from it. As a result, the platform should restart the application.

### 6.2. Readiness State

- readiness 상태란 어플리케이션이 트래픽을 핸들링 할 수 있는 상태인지를 나타내는 값이다
- failing readiness 는 지금 당분간은 애플리케이션으로 트래픽이 전송되면 안된다는 것을 의미하는데 CommandLineRunner 와 ApplicationRunner 컴포넌트들이 처리되는 시점이나 추가적인 트래픽이 발생해 애플리케이션이 너무 바쁘다고 판단될 때면 언제던지 발생할 수 있다.

```java
public enum ReadinessState implements AvailabilityState {

	/**
	 * The application is ready to receive traffic.
	 */
	ACCEPTING_TRAFFIC,

	/**
	 * The application is not willing to receive traffic.
	 */
	REFUSING_TRAFFIC

}
```

- An application is considered ready when it's {@link LivenessState live} and willing to accept traffic.
- "Readiness" failure means that the application is not able to accept traffic and that the infrastructure should stop routing requests to it.

### 6.3. Managing the Application Availability State

- 컴포넌트들은 현재 가용성 상태를 언제던 확인해 볼 수 있다.
ApplicationAvailability 인터페이스를 주입 받아서 필요한 메서드를 호출하면 된다
- 상태가 업데이트 되는 것을 리스닝 하고 싶으면 예를 들어 Readiness state 값이 업데이트 될 때 파일에 export 해서 쿠버네이트가 해당 파일을 열어 볼 수 있게끔 하려면 `AvailabilityChangeEvent<ReadinessState>` 를 사용할 수 있다.

```java
@Component
public class ReadinessStateExporter {

    @EventListener
    public void onStateChange(AvailabilityChangeEvent<ReadinessState> event) {
        switch (event.getState()) {
        case ACCEPTING_TRAFFIC:
            // create file /tmp/healthy
        break;
        case REFUSING_TRAFFIC:
            // remove file /tmp/healthy
        break;
        }
    }

}
```

- 어플리케이션이 깨져서(broken) 회복(recover)될 수 없을 때 state 값을 업데이트 하려면 아래같이 하면 된다.

```java
@Component
public class LocalCacheVerifier {

    private final ApplicationEventPublisher eventPublisher;

    public LocalCacheVerifier(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    public void checkLocalCache() {
        try {
            //...
        }
        catch (CacheCompletelyBrokenException ex) {
            **AvailabilityChangeEvent.publish(this.eventPublisher, ex, LivenessState.BROKEN);**
        }
    }

}
```

## 7. Application Events and Listeners

- ContextRefreshedEvent 같은 일반적인 스프링 프레임워크에서 사용하는 이벤트 외에도, SpringApplication 는 몇 추가적인 이벤트들을 발생시킨다.
- 몇 이벤트들은 ApplicationContext 가 생성되기 전에 발생하는데, 따라서 빈으로 그 이벤트들을 들을 수 없을 수 있다. 이런 이벤트 리스너들은 `SpringApplication.addListeners()` 메소드나 `SpringApplicationBuilder.listeners()` 메소드로 등록할 수 있다.
- 이런 리스너들을 자동으로 등록하고 싶다면, `META-INF/spring.factories` 파일에 등록할 수 있다.

```yaml
org.springframework.context.ApplicationListener= \
com.example.project.MyListener
```

- 발생하는 시점과 원인에 따라 이벤트를 아래와 같이 나눠 볼 수 있다. 궁금한 이벤트가 있다면 더 찾아보도록 하자
    - ApplicationStartingEvent
    - ApplicationEnvironmentPreparedEvent
    - ApplicationContextInitializedEvent
    - ApplicationPreparedEvent
    - ApplicationStartedEvent
    - AvailabilityChangeEvent
    - ApplicationReadyEvent
    - AvailabilityChangeEvent
    - ApplicationFailedEvent
    - ApplicationPreparedEvent
    - ApplicationStartedEvent
    - WebServerInitializedEvent
    - ServletWebServerInitializedEvent
    - ReactiveWebServerInitializedEvent
    - ContextRefreshedEvent

## 8. Web Environment

- SpringApplication 은 적당한 타입의 ApplicationContext 를 만들려고 시도한다
- WebApplicationType 인지 여부를 판단하는 알고리즘은 다음과 같다
    1. 만약 Spring MVC 를 사용중이라면, AnnotationConfigServletWebServerApplicationContext 타입이 사용된다
    2. 만약 Spring MVC 를 사용하지 않고, Spring WebFlux 를 사용중이라면 AnnotationConfigReactiveWebServerApplicationContext 타입이 사용된다
    3. 그렇지 않으면 AnnotationConfigApplicationContext 가 사용된다.
- 즉 Spring MVC 와 Spring WebFlux 의 WebClient 를 같은 애플리케이션에서 사용하면 Spring MVC 가 기본값으로 사용 될 것이라는 뜻이다.
- SpringApplication 의 setWebApplicationType(WebApplicationType) 메서드를 호출해서 쉽게 오버라이딩 할 수 있다.
- 혹은 setApplicationContextClass(...) 메서드도 존재한다

JUnit test 에서 SpringApplication 를 사용할 때 `setWebApplicationType(WebApplicationType.NONE)` 를 사용하는게 바람직한 상황이 자주 생긴다.

## 9. Accessing Application Arguments

- SpringApplication.run(...) 에 전달되는 아규먼트들에 접근해야 하는 경우 ApplicationArguments 를 빈으로 주입받아 확인해 볼 수 있다.

```java
import org.springframework.boot.*;
import org.springframework.beans.factory.annotation.*;
import org.springframework.stereotype.*;

@Component
public class MyBean {

    @Autowired
    public MyBean(ApplicationArguments args) {
        boolean debug = args.containsOption("debug");
        List<String> files = args.getNonOptionArgs();
        // if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
    }

}

```

## 10. Using the ApplicationRunner or CommandLineRunner

- SpringApplication 이 시작될 때 실행해야 할 코드가 있다면 `ApplicationRunner` 나 `CommandLineRunner` 인터페이스를 구현하는 방법이 있다.
- 두 인터페이스는 run(...) 메서드를 사용하면 되는데, 이 메소드는 SpringApplication.run(...) 실행이 완료되기 직전에 호출된다.
- CommandLineRunner 의 경우 application arguments 를 스트링 배열의 형태로 전달받고, ApplicationRunner 는 ApplicationArguments 인터페이스를 빈으로 주입해서 아규먼트를 사용할 수 있다.

    ```java
    import org.springframework.boot.*;
    import org.springframework.stereotype.*;

    @Component
    public class MyBean implements CommandLineRunner {

        public void run(**String... args**) {
            // Do something...
        }

    }
    ```

- 만약 CommandLineRunner 나 ApplicationRunner 가 여러개 있어서 순서를 정의해야 하는 경우라면, `org.springframework.core.Ordered` 인터페이스를 구현하거나 `org.springframework.core.annotation.Order` 어노테이션을 사용하면 된다.

## 11. Application Exit

- SpringApplication 은 shutdown hook 을 JVM 에 등록해 놓음으로써 gracefully 한 exit 방식을 제공한다.
- 여기서 표준 스프링 라이프사이클 콜백 함수들이 사용 될 수 있다. 
(DisposableBean 인터페이스나 @PreDestory 어노테이션 등)
- 빈들은 org.springframework.boot.ExitCodeGenerator 인터페이스를 구현함으로써 SpringApplication.exit() 이 호출될 때 특정한 종료 코드를 응답하게끔 할 수 있다. 이 종료 코드값들은 System.exit() 에 넘겨지고 System.exit() 의 상태코드로 반환된다.

```java
@SpringBootApplication
public class ExitCodeApplication {

    @Bean
    public ExitCodeGenerator exitCodeGenerator() {
        return () -> 42;
    }

    public static void main(String[] args) {
        System.exit(SpringApplication.exit(SpringApplication.run(ExitCodeApplication.class, args)));
    }

}
```

- ExitCodeGenerator 인터페이스에서 구현된 getExitCode() 메서드를 사용해서 특정 예외가 발생할 때를 처리할 수 있다.

## 12. Admin Features

- spring.application.admin.enabled 프로퍼티를 이용하면 admin 과 관련된 feature 들을 활성화 시킬 수 있다.

## 13. Application Startup tracking

- 애플리케이션 startup 시기 동안 SpringApplication 과 ApplicationContext 는 애플리케이션 라이프사이클, 빈 라이프 사이클, 애플리케이션 이벤트 처리와 관련된 여러가지 일을 처리한다.
- ApplicationStartup 을 사용해서 어플리케이션 startup 시퀀스를 추적할 수 있다.
- 이 데이터들을 이용해서 프로파일링 목적에 맞춰 쓰거나 애플리케이션 구동 프로세스를 더 잘 이해할 수 있다.
- ApplicationStartup 구현체를 아래와 같이 설정할 수 있다.

```java
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(MySpringConfiguration.class);
    app.setApplicationStartup(new BufferingApplicationStartup(2048));
    app.run(args);
}
```

# ApplicationContext hierarchy

## ApplicationContext?

- 스프링은 여러 기능을 제공하는데 그 중 핵심을 담당하는 건, 바로 빈 팩토리 또는 애플리케이션 컨텍스트라고 불리는 것이다.
- 스프링에서 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트를 빈 팩토리라고 부른다. 보통 빈 팩토리보다는 이를 좀 더 확장한 애플리케이션 컨텍스트를 주로 사용한다. 애플리케이션 컨텍스트는 IoC 방식을 따라 만들어진 일종의 빈 팩토리라고 생각하면 된다.
(어플리케이션 컨텍스트 = 빈 팩토리 + IoC 방식의 사용)
- 빈 팩토리와 애플리케이션 컨텍스트라는 용어가 동일하다고 생각하면 되는데, 빈 팩토리라고 말할 때는 빈을 생성하고 관계를 설정하는 IoC의 기본 기능에 초점을 맞춘 것이고, 애플리케이션 컨텍스트라고 말할 때는 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 IoC 엔진이라는 의미가 좀 더 부각된다고 보면 된다.
- 애플리케이션 컨텍스트는 별도의 정보를 참고해서 빈의 생성, 관계설정 등의 제어 작업을 총괄한다. 예를 들어 어떤 클래스의 오브젝트를 생성하고 어디에서 사용하도록 연결해줄 것인가 등에 대한 정보를 참고한다.

```java
@Configuration
public class DaoFactory {

	@Bean
	public UserDao userDao() {
		return new UserDao(connectionMaker());
	}
	
	@Bean
	public ConnectionMaker connectionMaker() {
		return new DConnectionMaker();
	}

}
```

```java
ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
UserDao dao = context.getBean("userDao", UserDao.class);
```

## Applicationcontext hierarchy

1. An ApplicationContext cannot have more than 1 parent ApplicationContext.
2. When a given ApplicationContext cannot resolve a bean, it will pass on the resolution request to its parent.
3. The parent of an ApplicationContext is specified in its constructor.

The classic use-case for this is when you have multiple Spring `DispatcherServlet` within a single webapp, with each of these servlets having their own app context, but which need to share beans between them. In this case, you add a 3rd context at the level of the webapp, which is the parent of each of the servlet appcontexts.

You can take this pattern further, for example if you have multiple webapps bundled into a single JavaEE EAR. Here, the EAR can have its own context, which is the parent of the individual webapp contexts, which is the parent of the servlet contexts, and so on. You have this hierarchy of responsibility.

In other situations, the context structure is dictated by some other factor. For example, Spring Security is independent of Spring MVC, and requires its configuration beans to go in the webapp context. If you want to use Spring MVC with it, then the config for that has to go into the servlet context, which has the root webapp context as its parent.

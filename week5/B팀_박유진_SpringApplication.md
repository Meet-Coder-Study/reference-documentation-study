## Spring Application
--

Spring Application 실행 시, log level이 default로 INFO로 설정되어 있지만, 만약에 다른 [Log Levels](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-custom-log-levels)로 설정하고 싶다면 설정 가능하다. 기동 로그를 끄고 싶다면 ```spring.main.log-startup-info```를 ```false```로 설정하면 된다. 추가적으로 로깅하고싶다면 ```SpringApplication ```의 서브 클래스인 ```logStartupInfo(booelan)```에서 재정의할 수 있다.

### 1.1 Startup Failure
 
문제가 생겨 애플리케이션을 시작하지 못한 경우 ``FailureAnalyzers``를 통해 오류 메시지와 문제를 해결하기 위한 구체적인 행동이 제시된다.
또한, Spring Boot는 여러가지 ``FailureAnalyzer`` 구현들을 제공하기 때문에 [직접 추가 가능](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/howto.html#howto-failure-analyzer)하다.
 
 ```shell script
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```

### 1.2 Lazy Initialization

Spring Application은 application을 느리게 초기화 할 수 있다 Lazy Initialization이 활성화 되면 시작할 때 Bean 객체가 생성되는게 아니라 필요에 따라 Bean이 생성됩니다. 그렇기 때문에 Lazy Initialization 기능을 활성화시키면 Application 시작이 시작되는 시간을 단축 시킬 수 있다 단, 필요에 따라 Bean 객체가 생성되기 때문에 웹 애플리케이션의 경우 HTTP 요청이 들어오기 전까지 웹 관련 Bean 객체가 초기화되지 않습니다.

Lazy Initalization이 활성화되면 Application의 시작 시간을 단축시킬 수 있는 장점도 있지만 Application이 시작 할 때 발생될 문제들이 실행 중에 발생될 수 있기 때문에 문제 발견을 지연시킬 수 있다는 단점도 존재합니다.   
잘못 구성된 Bean 객체가 Lazy Initalization이 활성화되어 있지 않을 때 초기화 된다면, Application 시작 과정에서 문제를 발견 할 수 있다 또한, JVM이 실행되는 Application의 Bean 객체를 모두 수용할 수 있는 메모리가 있는지 확인해야한다.
이러한 이유들로 ``Lazy Initalization을 기본적으로 활성화되어있지 않으며`` 이 기능을 활성화 시키려면 활성화 전에 JVM heap memory 조정 하는 것이 좋다.

>__*특정 Bean에 대해서만 Lazy Initailization 기능을 비활성화 하려면 ``@Lazy(false)`` 주석을 사용하여 명시적으로 설정할 수 있다.*__

### 1.3 Customizing the Banner

Application 시작 시 출력되는 배너도 설정이 가능하다.

|Variable|Description|
|:------|---
|${application.version}|MANIFEST.MF에 설정한 버전 정보|
|${application.formatted-version}|MANIFEST.MF에 설정한 버전 정보 & 보여줄 때 formatting|
|${spring-boot.version}|Spring Boot 버전 정보|
|${spring-boot.formatted-version}||
|${application.title}|MANIFEST.MF에 설정한 Application 이름|

> 이 외에도 ``org.springframework.boot.Banner`` interface를 implement 받아서 실제로 printBanner()를 구현하면 custom 가능하다.

### 1.4 Customizing SpringApplication

 [SpringApplication](https://docs.spring.io/spring-boot/docs/2.4.3/api/org/springframework/boot/SpringApplication.html) instance를 생성해서 설정들을 customizing이 가능하다.
 
 기존 default SpringApplication을 사용한 예제
 ```java
  @Configuration
  @EnableAutoConfiguration
  public class MyApplication  {
 
    // ... Bean definitions
 
    public static void main(String[] args) {
      SpringApplication.run(MyApplication.class, args);
    }
  }
```
instance를 생성하여 설정 드을 customizing 한 SpringApplication 예제
 ```java
public static void main(String[] args) {
   SpringApplication application = new SpringApplication(MyApplication.class);
   // ... customize application settings here
   application.setBannerMode(Banner.Mode.OFF);
   application.run(args)
 }
```

``application.properties``를 활용해서 SpringApplication 설정하는것도 가능하다. [Externalized Configuration](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-external-config) 활용하는 것도 가능하다.
구성에 관한 내용은 [``SpringApplication`` JavaDoc](https://docs.spring.io/spring-boot/docs/2.4.3/api/org/springframework/boot/SpringApplication.html)을 참조하면된다.

### 1.5 Fluent Builder API

만약, ``ApplicationContext`` hierarchy 빌드가 필요하거나 fluent builder API를 사용하는 것을 선호한다면 ``SpringApplicationBuilder``를 사용할 수 있다.

``SpringApplicationBuilder``는 여러 method chaining을 통해서(``parent``, ``child`` method 포함) 계층을 만들 수 있다.

```java
new SpringApplicationBuilder()
        .sources(Parent.class)
        .child(Application.class)
        .bannerMode(Banner.Mode.OFF)
        .run(args);
```

### 1.6 Application Availability

Spring Boot에는 ``liveness``, ``readiness`` 와 같은 일반적인 Application의 가용 상태를 지원한다. 만약, Spring Boot의 ``actuator``를 사용한다면 Application 가용 상태에 대한 정보들을 노출시켜 Application 모니터링을 할 수 있다.

#### 1.6.1 Liveness State

Liveness State는 Application이 올바르게 동작할 수 있도록 허용하는 상태인지, 또는 현재 문제가 있더라도 자체적으로 복구가 가능한지에 대한 상태를 의미한다.
Liveness State가 깨진다면, Application 스스로 복구가 불가능하기 때문에 인프라 영역에서 application을 재시작 해야함을 의미한다.

> 일반적으로 ``Liveness State``는 [Health checks](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/production-ready-features.html#production-ready-health)같은 외부적인 요소가 기반이 되지 않는다. 외부 시스템에 문제가 있는 경우 플랫폼 전체적으로 재시작 되거나 단계적으로 문제가 발생하게 되기 때문.

#### 1.6.2  Readiness State

Readiness State는 Application이 traffic을 처리 할 준비가 되었는지를 보여주는 상태이다. Readiness State가 깨진다면 현재 traffic을 application으로 라우팅하지 말아야 한다고 플랫폼에 알려준다.

#### 1.6.3 Managing the Application Availability State

``ApplicationAvailability`` interface를 통해서 Application 상태를 더 자주 업데이트 할 수 있다.

### 1.7 Application Events and Listeners

``SpringApplication``은 ``ContextRefreshedEvent``와 같은 스프링 프레임워크가 발생시키는 이벤트들과 더불어 몇몇 Application 이벤트들을 발생시킨다.

> ``ApplicationContext``가 만들어지기 전에 발생하는 Event도 있으므로(``@Bean`` 사용 불가) 해당 Event에 ``SpringApplication.addListeners()``, ``SpringApplicationBuilder.listeners()``  방법 등으로 리스너를 등록 할 수 있다.
>
> 리스너 자동 등록하려면 ``META-INF/spring.factories`` 파일을 프로젝트에 추가하고 거기에 ``org.springframework.context.ApplicationListener`` key를 이용해서 리스너들을 등록한다. 


 Application Event들은 실행 시 아래의 순서대로 보내진다.
 1. Application이 시작되고 어떤 처리도 하기 전에 ``ApplicationStartingEvent``가 등록된 리스너들과 Initializer들을 제외하고 보내진다.
 2. ``ApplicationEnvironmentPreparedEvent``는 Context에서 사용될 Environment을 찾았지만 아직 Context가 생성되기 전에 보내진다.
 3. ``ApplicationContextInitializedEvent``는 ``ApplicationContext``가 준비되고 ``ApplicationContextInitializers``가 호출되었지만 정의된 bean이 로딩되기 전에 보내진다.
 4. ``ApplicationPreparedEvent``은 정의된 빈들이 로딩되고 refresh가 시작되기 바로전에 보내진다.
 5. ``ApplicationStartedEvent``는 Context가 refresh되고 Application과 command-line runner가 호출되기 전에 보내진다.
 6. ``AvailabilityChangeEvent``는 ``LivenessStat.CORRECT`` 와 함께 바로 보내져서 application이 live 상태임을 나타낸다.
 7. 어떤 [application and command-line runners](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-command-line-runner)라도 호출되면 ``ApplicationReadyEvent``가 보내진다.
 8. ``AvailabilityChangeEvent``는 ``ReadinessState.ACCEPTING_TRAFFIC``와 함께 바로 보내져서 application이 이제 서비스를 수행할 수 있는 상태임을 나타낸다.
 9. ``ApplicationFailedEvent``는 시작하는 도중에 예외가 발생하면 보내진다.
 
 > Application이 ``SpringApplication`` instance 계층 구조를 사용하는 경우 이벤트 리스너는 같은 타입의 application event 객체들을 여러개 받게 된다. 따라서 꼭! 리스너는 Context 비교를 통해서 자신의 Context에서 발생한 이벤트인지 자식 Context에서 발생한 이벤트인지 구별해야 한다.(``ApplicationContextAware``를 구현하거나 리스너가 Bean일 경우 ``@Autowired``을 사용할 수 있다.)
>
> 
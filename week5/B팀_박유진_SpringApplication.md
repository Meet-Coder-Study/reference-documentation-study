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
# SpringApplication
## SpringApplication
(1) Code Example
```java
public static void main(String[] args) {
    SpringApplication.run(MySpringConfiguration.class, args);     
}
```

(2) 한 줄 기능 정리
<br>
- main() 메서드에서 스프링 부트 어플리케이션이 Boot 하는데 편리한 기능들 제공

(3) 어떤 기능들이 실행될까  ??

<img width="700" src="https://user-images.githubusercontent.com/60383031/117107998-806d4100-adbd-11eb-9074-1f88dd40b6e4.png">

- listener 실행, application.yml 읽기, 배너 프린트 등 어플리케이션이 부트될 떄 실행되는 기능들이 포함 됨  
<br>
  

(4) APPLICATION FAILED TO START
- 호출 과정
    - 1. Application 실행이 실패한다면 위에서 확인한 run 메서드에서 catch안에 있는 handleRunFailure() 호출
    - 2. finally안에 있는 reportFailure() 메소드가 호출

    <img width="700" src="https://user-images.githubusercontent.com/60383031/117108760-9cbdad80-adbe-11eb-83c7-3b3418ca995e.png">

    - 3. FailureAnalyzers 클래스에 있는 reportExecption() 호출

    <img width="700" src="https://user-images.githubusercontent.com/60383031/117110479-1f476c80-adc1-11eb-9291-d284a97d842d.png">

    - 4. report() 호출
    
     <img width="700" src="https://user-images.githubusercontent.com/60383031/117110628-646b9e80-adc1-11eb-8984-49fc9365e46a.png">
  
    - 5. LoggingFailureAnalysisReporter의  reporter.report() 호출

    <img width="700" src="https://user-images.githubusercontent.com/60383031/117110970-e360d700-adc1-11eb-98ef-9735700c29ed.png">
  
    - 6. report()가 호출 되면 최종적으로 buildMessage()가 호출
    
    <img width="700" src="https://user-images.githubusercontent.com/60383031/117109612-e22eaa80-adbf-11eb-9251-bc33b878e6cf.png">
  

## Lazy Initialization
```
- 어플리케이션이 실행될 때 Bean을 생성하는 것이 아닌 호출될 때 Bean으 생성하는 기능
- spring.main.lazy-initialization=true 옵션 설정
- @Lazy 어노테이션을 사용하여 특정 Bean 에 대해서 지연 설정을 할 수 있다.
- 어플리케이션 실행 시점에 Bean을 생성하지 않기 때문에 컴파일 타임에서 발생할 오류를 검증하지 못할 것 같다.
```


## Application Availability
(1) Application Availability ??
- Kubernetes를 지원하기 위해서 도입된 기능 중 하나이다.
- 어플리케이션의 가용성을 체크하는데 사용되는 기능들을 제공하며 Liveness State, Readiness State 기능을 제공한다.

(2) Liveness State
- Kubernetes
  - LivenessProbe를 사용하여 컨테이너가 동작 중인지 여부 확인 -> 살아있는지 확인
  - 실패 -> 컨테이너 종료 후 재실행
  - 아래와 같이 매니페스트 파일을 작성해서 해당 Probe를 체크할 수 있다.
  ```
  livenessProbe:
    httpGet:
      path: /api/v1/health/hello
      port: 8080
    initialDelaySeconds: 60
    periodSeconds: 10
  ```  
  
- Spring Boot
  - LivnessState 객체를 아래와 같이 제공
  
  <img width="700" src="https://user-images.githubusercontent.com/60383031/117163175-5ee07980-adfe-11eb-86b2-2d4881eab76e.png">

(3) Readiness State
- Kubernetes
  - Readiness State를 서용하여 컨테이너가 요청을 처리할 준비가 되었는지 확인
  - 실패 -> 컨테이너 제거, 트래픽 연결 해지
  - 아래와 같이 매니페스트 파일을 작성해서 해당 Probe를 체크할 수 있다.
  ```
  readinessProbe:
    exec:
      command:
        - cat
        - /tmp/healthy
    initialDelaySeconds: 1
    periodSeconds: 10
  
  ```

- Spring Boot
  - ReadinessState 객체를 아래와 같이 제공

  <img width="700" src="https://user-images.githubusercontent.com/60383031/117165187-3b1e3300-ae00-11eb-8bf6-4b01720b019d.png">

(4) Spring boot 에서 사용하는 법 
```java
@RestController
@RequestMapping("/api/v1/health")
public class probeChecker {

    @Autowired
    private ApplicationAvailability availability;
    
    @GetMapping("/readinessState")
    public ResponseEntity<ReadinessState> checkReadinessState() {
        return ResponseEntity.ok(availability.getReadinessState());
    }

    @GetMapping("/livenessState")
    public ResponseEntity<LivenessState> checkLivenessState() {
        return ResponseEntity.ok(availability.getLivenessState());
    }
}
```

(4) Actuator
- Actuator를 사용하여 아래와 URL을 호출하여 체크 가능
  - http://localhost:8080/actuator/health/readiness
  - http://localhost:8080/actuator/health/liveness


- 참고
https://spring.io/blog/2020/03/25/liveness-and-readiness-probes-with-spring-boot




## Application Events and Listeners
- ApplicationContext 생성 시점을 기준으로 트리거로 삼아 여러가지 이벤트들을 실행 가능 
- 비슷한 기능으로 @PostConstruct와 @PreDestory가 있지민, 미묘한 차이가 있다. 이 기능은 Bean의 생성(파괴) 시점을 기준으로 실행된다.
- 또 다른 비슷한 기능으로 해당 공식문서 1.10에서 소개하는 ApplicationRunner CommandLineRunner eventListener도 유사한 기능으로 사용할 수 있다.

## Web Environment
- ApplicationContext(스프링 컨테이너) 인터페이스의 구현체 종류에 대하여 설명하고 있다.
- Spring MVC인 경우 `AnnotationConfigServletWebServerApplicationContext` WebFlux인 경우 `AnnotationConfigReactiveWebServerApplicationContext`를 사용
- 그 외는 `AnnotationConfigApplicationContext` 를 사용한다.
- 참고로 Spring boot 2.3.10 기준으로 ApplicationContext 구현체는 현재 총 38개이다.
  
  <br>
  <img width="700" src="https://user-images.githubusercontent.com/60383031/117167412-30649d80-ae02-11eb-857c-5772615b6b27.png">


- AnnotationConfigServletWebServerApplicationContext 의존 구조
  
  <br>
  <img width="700" src="https://user-images.githubusercontent.com/60383031/117167930-ac5ee580-ae02-11eb-993c-3049b8f5edf0.png">

## Application Startup tracking
- Bean 혹은 이벤트 라이프 사이클을 트랙킹 하는데 사용 
- 예제
```java
public static void main(String[] args) {
    SpringApplication application = new SpringApplication(MyApplication.class);
    application.setApplicationStartup(new BufferingApplicationStartup(2048));
    application.run(args);
}
```
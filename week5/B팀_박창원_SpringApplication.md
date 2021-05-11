## 스프링 부트의 늦은 초기화(Lazy Initialization)



SpringApplication은 초기화를 늦게(lazily) 수행할 수 있다.

늦게 초기화를 한다는 것은 Bean의 생성 시점을 두고 말하는 것이다.

늦은 초기화가 활성화되면, 빈들은 애플리케이션 초기화 시점 이후에 **필요한 시점**에 생성된다.

늦은 초기화의 장점은 무엇일까? 

- 애플리케이션의 시작 속도가 빨라진다.
- 특히, web과 관련된 빈들은 HTTP request를 받기 전까지 생성되지 않으므로 더 빠르다.



### 애플리케이션 Lazy 초기화 속도 측정

실습은 spring의 petclinic 프로젝트를 사용하였다.

- Lazy 초기화 설정하지 않을 경우, 2.815초

```
2021-05-01 10:25:51.945  INFO 98669 --- [  restartedMain] o.s.s.petclinic.PetClinicApplicationKt   : Started PetClinicApplicationKt in 2.815 seconds (JVM running for 3.547)
```

- Lazy 초기화 설정 시 2.798초

```
2021-05-01 10:23:17.469  INFO 97598 --- [  restartedMain] o.s.s.petclinic.PetClinicApplicationKt   : Started PetClinicApplicationKt in 2.798 seconds (JVM running for 3.588)
```



늦은 초기화의 단점은 무엇일까?

- 애플리케이션 빈에 문제가 있는 것을 그 빈을 사용하는 시점에 알 수 있다.
- 애플리케이션 빈들을 생성가능한 JVM의 메모리를 초기화 시점에 체크할 수 없다.(따라서, JVM 힙 사이즈를 의도적으로 맞춰 설정하지 않는 이상 lazy initialization을 추천하지 않는다.)



lazy 초기화를 위한 방법은 총 세가지다.

- `SpringApplicationBuilder` 의 `lazyInitialization` 메서드

-  `SpringApplication`의 `setLazyInitialization` 메서드
-  프로퍼티를 `spring.main.lazy-initialization=true` 로 설정



만약 빈을 부분적으로 늦은 초기화를 설정하려면 어떻게 해야할까?

@Lazy 어노테이션으로 설정할 수 있다.



### 늦은 초기화 오류 실습 

```kotlin
@Controller
@Lazy
class OwnerController(val testBean: TestBean, val owners: OwnerRepository, val visits: VisitRepository) {
	// ...
}

@Component
class TestBean {}
```

OwnerController에 @Lazy 를 추가하고 `/owners/new` 요청을 해보자.

로그 레벨을 **debug** 로 두고 로그를 확인하면 다음과 같이 출력된다.

```
2021-05-01 10:19:04.583 DEBUG 95221 --- [io-18080-exec-1] o.s.b.f.s.DefaultListableBeanFactory     : Creating shared instance of singleton bean 'ownerController'
2021-05-01 10:47:00.778 DEBUG 9349 --- [io-18080-exec-1] o.s.b.f.s.DefaultListableBeanFactory     : Autowiring by type from bean name 'ownerController' via constructor to bean named 'testBean'
```

실제 요청 시점에 ownerController의 빈을 생성하는 것을 알 수 있다.

늦은 초기화가 적용된 빈에 주입된 bean을 테스트하기 위해 TestBean 컴포넌트를 작성하였다.

이렇게 lazy bean에 주입된 bean은 lazy하게 생성되지 않으며, lazy bean이 생성되는 시점에 `DefaultListableBeanFactory` 가 자동으로 주입(Autowiring) 해줌을 알 수 있다.





## 애플리케이션 사용가능성 (Application Availability)

쿠버네티스와 같은 인프라 플랫폼에 스프링부트를 배포한다면, 애플리케이션의 상태를 플랫폼에 알려주는 것이 중요하다.

스프링 부트는 liveness와 readiness 사용가능 상태를 지원한다.

만약 actuator 라이브러리를 사용한다면 endpoint 상태 그룹으로 이 두가지 상태를 노출할 수 있따.

또한, 사용가능 상태를 알려면, ApplicationAvailability 인터페이스를 여러분의 빈에 주입하면 된다.



### Liveness State

Liveness 상태는 애플리케이션이 동작을 정확히 하는지에 대한 내부 상태를 말해준다. 

Liveness 상태가 망가지면, 애플리케이션은 복구할 수 없다는 것을 의미하며, 인프라스트럭쳐는 애플리케이션을 재시작해야한다.

일반적으로, Liveness 상태는 health check와 같은 외부 검사에 기반하면 안된다.
만약 외부 검사에 기반한다면, 외부 시스템(API, Cache, Database)가 실패할 때, 매우 많은 재시작을 야기하며, 플랫폼에 걸쳐 실패가 전파된다.
스프링 애플리케이션의 내부 상태는 ApplicationContext가 대표한다.
애플리케이션 컨텍스트가 성공적으로 시작되면, 스프링 부트는 애플리케이션을 유효한 상태로 둔다.
컨텍스트가 refreshed 되자마자 애플리케이션은 살아있는 상태(live)로 간주된다.

자세한 사항은 [Spring Boot application lifecycle and related Application Events](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-application-events-and-listeners)를 참고하자 



### Readiness State

- Readiness는 애플리케이션이 트래픽을 받을 준비가 되어있음을 말해준다.
- Readiness가 실패하면 플랫폼은 트래픽을 라우트하지 말아야 한다.
- Readiness 실패는 대부분 초기시작시 발생한다.
- CommandLineRunner나 ApplicationRunner 컴포넌트가 처리중일 때, 또는 애플리케이션이 추가적인 트래픽을 받기에 너무 바쁠때이다.

애플리케이션이 준비되었다고 간주되는 때는, Application runner 또는 commandline runner가 호출되었을 때다.
자세한 사항은 [Spring Boot application lifecycle and related Application Events](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-application-events-and-listeners)를 참고하자



애플리케이션의 컴포넌트는 현재 사용가능 상태를 언제든지 호가인할 수 있다.

이를 위해 `ApplicationAvailability` 인터페이스를 주입하고 이 인터페이스의 메서드를 호출하면 된다.

 예를 들어, 다음은 쿠버네티스가 exec Probe를 실행하여 파일을 찾기 위해 애플리케이션의 `Readiness`  상태를 노출시킬 수 있다.

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

또는, 애플리케이션 상태를 상황에 맞춰 수정할 수 있다.

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
            AvailabilityChangeEvent.publish(this.eventPublisher, ex, LivenessState.BROKEN);
        }
    }

}
```



Readiness State가 언제 바뀌는지 확인해보자.

ReadinesState를 바꾸는 부분은 EventPublishingRunListener가 수행한다.

```java
public class EventPublishingRunListener implements SpringApplicationRunListener, Ordered
```



running 메서드에 디버그를 찍어서 확인해보면, ApplicationRunner 인터페이스를 구현한 빈 이후에  running 메서드가 실행된다.

이 running 메서드가 애플리케이션의 상태를 `ACCEPTING_TRAFFIC` 으로 변경함을 알 수 있다.

```java
@Override
public void running(ConfigurableApplicationContext context) {
  context.publishEvent(new ApplicationReadyEvent(this.application, this.args, context));
  AvailabilityChangeEvent.publish(context, ReadinessState.ACCEPTING_TRAFFIC);
}
```




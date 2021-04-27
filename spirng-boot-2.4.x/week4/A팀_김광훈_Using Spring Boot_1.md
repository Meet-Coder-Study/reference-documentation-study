# Using Spring Boot
## Spring Beans and Dependency Injection
```
- 공식 문서에서는 생성자 주입을 소개하고 있다.

@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    @Autowired
    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...
}


- 생성자가 딱 1개 있는 경우는 @Autowired를 생략할 수 있다.

@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...
}


- 생성자가 여러개 있는 상황에서 스프링은 어떤 생성자에 @Autowired 해야하는지 알 수 없다.


```
<br>

## Using the @SpringBootApplication Annotation
````
@SpringBootApplication
- (1) @SpringBootConfiguration: 현재 클래스를 구성 클래스로 지정 
- (2) @EnableAutoConfiguration: 스프링 부트 자동 구성을 활성화 시킨다.
- (3) @ComponentScan: 컴포넌트 스캔을 확성화 한다. --> @Component 애노테이션이 붙은 클래스를 스캔해서 스프링 Bean 으로 등록 --> @Configuration 소스코드에도 @Component 어노테이션이 들어 있다.

코드
@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
````
<br>

## Running Your Application
```
- 공식 문서에서는 4가지 방법을 소개한다.
(1) java -jar target ...
(2) mvn spring-boot : run
(3) gradle bootRun, 
    - 필자는 ./gradlew clean bootRun -P ... 사용
(4) 핫 스왑핑 - JRebel Tool
```
<br>

## Packaging Your Application for Production
actuator
- 아래와 같이 시스템 리소스를 확인하는 용도로 사용할 수 있다.

 <img width="656" src="https://user-images.githubusercontent.com/60383031/116250775-ff082400-a7a8-11eb-9fb3-84f64694aebb.png">

- 관련 링크
    - https://aboullaite.me/an-introduction-to-spring-boot-actuator/
    - https://www.popit.kr/spring-actuator-%EA%B8%B0%EC%B4%88-%EC%84%A4%EC%A0%95-intellij-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/

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

- 의문점
    - 지금까지 스프링을 개발하면서 생성자 주입을 할 때 생성자 위에 @Autowired 를 붙여 본 경험이 없었다.
    - 검색을 해본 결과 Spring 4.3 버전 이후로는 생략 가능하다고 한다.
    - 참고: https://leejisoo860911.tistory.com/2
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

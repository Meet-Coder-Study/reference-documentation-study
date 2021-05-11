# Using Spring Boot
## Spring Beans and Dependency Injection
- 공식 문서에서는 생성자 주입을 소개하고 있다.
```
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

- Dependency Injection 종류
```
(1) 생성자 주입
- 객체가 생성될 떄 생성자를 호출하면서 의존관계 주입 발생
- final 키워드를 사용함으로서 불변성 보장
- 순환 참조를 컴파일 타임에 잡을 수 있는 장점이 있다.


(2) 수정자 주입
- 선택, 변경 가능이 있는 의존관계에서 사용 -> 외부에서 강제 호출 -> 잘 사용하지는 않음
- 예시
private MemberRepository memberRepository;

@Autowired
public void setMemberRepository(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
}

 
(3) 필드 주입
- @Autowired MemberRepository memberRepository 같은 형태
- 코드는 간결하지만 외부에서 변경이 불가능하다. 
  - 즉 테스트하기가 힘들다. -> 가짜 객체 생성할 방법이 없다. -> 순수한 자바 코드로 테스트 하기 어렵고 스프링 컨테이너에서 해야한다.
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
- 아래와 같이 어플리케이션에 대한 메트릭 및 통계 정보를 확인하는 용도로 사용할 수 있다.

 <img width="656" src="https://user-images.githubusercontent.com/60383031/116250775-ff082400-a7a8-11eb-9fb3-84f64694aebb.png">

- 의존성 추가
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
}

```


- Actuator Endpoint
```
(1) /health
- 애플리케이션 상태 정보 표시
- 응답 예시
{
   "status" : "UP"
}


(2) /info
- 애플리케이션 properties 정보 등 표시
- 응답 예시
{
  "app": {
      "version": "0.0.1-SNAPSHOT",
      "description": "Simple project of Spring Actuator with examples",
      "name": "Spring Actuator Example"
      }
  }
}

(3) /metrics
- 매트릭 정보를 표시 
- Memory, Heap, Thread 수 등
- 매트릭 응답 예시는 해당 글 액츄에이터 파트 맨 위에 이미지로 첨부하였음

(4) /trace
- 트레이스 정보 표시 -> Http request 추적
- 응답 예시 (참고 링킈: https://www.baeldung.com/spring-boot-actuator-http)
{
  "traces": [
    {
      "timestamp": "2019-08-05T19:28:36.353Z",
      "principal": null,
      "session": null,
      "request": {
        "method": "GET",
        "uri": "http://localhost:8080/echo?msg=test",
        "headers": {
          "accept-language": [
            "en-GB,en-US;q=0.9,en;q=0.8"
          ],
          "upgrade-insecure-requests": [
            "1"
          ],
          "host": [
            "localhost:8080"
          ],
          "connection": [
            "keep-alive"
          ],
          "accept-encoding": [
            "gzip, deflate, br"
          ],
          "accept": [
            "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8"
          ],
          "user-agent": [
            "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36 OPR/62.0.3331.66"
          ]
        },
        "remoteAddress": null
      },
      "response": {
        "status": 200,
        "headers": {
          "Content-Length": [
            "12"
          ],
          "Date": [
            "Mon, 05 Aug 2019 19:28:36 GMT"
          ],
          "Content-Type": [
            "text/html;charset=UTF-8"
          ]
        }
      },
      "timeTaken": 82
    }
  ]
}
```


- 관련 링크
    - https://aboullaite.me/an-introduction-to-spring-boot-actuator/
    - https://www.popit.kr/spring-actuator-%EA%B8%B0%EC%B4%88-%EC%84%A4%EC%A0%95-intellij-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/

# 10week Spring study

## Sending email

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-email

> JavaMailSender.java
org.springframework.mail.javamail

```
public class EmailService {

  private final JavaMailSender javaMailSender;

  void sendEmail(SimpleMailMessage msg) {

    try {
      javaMailSender.send(msg);
    } catch (MailException e) {
      e.printStackTrace();
      throw new InternalServerException(ErrorStatusCode.FAIL_TO_SEND_FAQ_EMAIL);
    }
  }
}
```

```gradle
        implementation 'org.springframework.boot:spring-boot-starter-data-mongodb'
        compile("com.querydsl:querydsl-mongodb:$querydsl_version")
        compile("com.querydsl:querydsl-apt:${querydsl_version}")
```
#  스프릥 세션


스프링 세션과 관련하여 예제를 진행하기 전 간단한 기초를 읽고 가즈아!

#### HTTP 프로토콜 특징
1. Connecionless
2. Stateless

비연결성, 상태값을 가지지 않는다

동작형태 -> 클라이언트/서버( 요청과 응답 <-포인트)
비연결성(상태 노유지 프로토콜)-> 소왓? -> 무작위를 상대로하는 서비스로는 최적이지
메시지 교환형태 프로토콜이야(클라이언트와 서버간의 http메세지 주고받으며 통신)

stateless라며? -> 쿠키 등장!

#### 쿠키와 세션
-> Http의 약점 보완
- 쿠키(클라이언트 로컬에 저장되는 키와 값) - 클라이언트 파일로 저장
- 세션(서버에서 일정 시간동안 저장되는 키와 값) - 서버에 저장

세션은 서버의 자원을 사용하기 때문에 마구잡이로 사용하다보면 서버 메모리가 감당할수 없게 되고 그로 인해 서비스 장애 또는 서비스가 느려 질수 있다. 그래서 쿠기를 사용해야 한당.

그럼 캐시는 모야?
캐시는 이미지(css,js파일 등 사용자 브라우저 즉 클라이언트 브라우저에 저장되는 것)


## 스프링 세션으로 HTTP 세션 다뤄보기

spring.io/projects/spring-session 참고하자!

스프링 세션은 세션 동기화를 위해 SPI(Service Provide interface)에 의존하는 서블릿 HTTP세션을 쉽게 대체 할수 있다.
SPI 구현체
- 레디스
- 아파치 지오드
- 헤이즐캐스트
- etc...

스프링 세션은 HTTP 세션 코드를 만질 필요 없이 스프링 세션을 설치해주기만 하면 된다.(간단하다.)
여러 개의 어플리케이션 노드가 실행되면 동일한 레디스 클러스터를 통해 세션 정보를 공유하므로 레디스의 강력한 상태 복제 기능의 혜택을 누릴수 있다.

>스프링 세션 장점
- 웹소켓을 통해 오가는 HTTP세션 지원
- 편리안 로그아웃 기능 지원
- 논리적으로 동일하지 않는 세션에 대한 접근 지원!! 즉 2개의 분리된 애플리케이션이 동일한 세션 상태를 공유 할수 있다.(놀랍지 않은가?)

https://spring.io/blog/2015/03/01/the-portable-cloud-ready-http-session 참고

#### 스프링 부트 세션 예제

> application.properties

```
server.session.timeout=5

##아래의 설정을 해두지 않으면
##Invocation of init method failed; nested exception is java.lang.IllegalArgumentException: No session repository could be auto-configured, check your configuration (session store type is 'null') 과 같은 에러 발생
spring.session.store-type=none
```

> Application with SessionController

```

@SpringBootApplication
public class DemoApplication {

 public static void main(String[] args) {
  SpringApplication.run(DemoApplication.class, args);
 }
}

@RestController
class SessionController {

 private final String ip;

 @Autowired
 public SessionController(@Value("${CF_INSTANCE_IP:127.0.0.1}") String ip) {
  this.ip = ip;
 }

 @GetMapping("/hi")
 Map<String, String> uid(HttpSession session) {
  // <1>
  UUID uid = Optional.ofNullable(UUID.class.cast(session.getAttribute("uid")))
   .orElse(UUID.randomUUID());
  session.setAttribute("uid", uid);

  Map<String, String> m = new HashMap<>();
  m.put("instance_ip", this.ip);
  m.put("uuid", uid.toString());
  return m;
 }

}
```

> /hi를 호출하게 되면
```
// 20180804172306
// http://localhost:8080/hi

{
  "instance_ip": "127.0.0.1",
  "uuid": "d90d218d-8eec-46e6-ad18-58da10ed5d27"
}
```
위의 데이터를 얻을수 있다.
(HTTP세션에 uid 값이 없으면  uuid를 생성하여 uid값으로 저장한다.) 몇 번을 동일하게 호출하더라도 같은 uuid 값을 얻을수 있다. 물론 서버를 재기동하게 되면 다른 uuid 값이 나온다.

<네이티브 클라우드 자바 5장 참고>

네이티브 클라우드 책 강력추천

[기타 스프링 시큐리티 관련]
https://beanbroker.github.io/2020/02/16/Spring/spring_security2/
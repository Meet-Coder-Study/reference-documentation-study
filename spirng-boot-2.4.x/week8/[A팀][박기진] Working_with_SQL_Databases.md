# 8week JPA

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-sql

## Working with SQL Databases

mysql 기준으로 테스트

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/study
    username: root
    password: password

```


## 1. JPA with audit

audit을 어디에 쓰면 좋을가?

여러개의 서버에서 같은 디비를 쓴다고 하였을 때 어디서로부터 업데이트가 되었는지에 대한 판단을 위해 쓰면 좋다

디비에 생성 및 업데이트 시간을 어플리케이션 레이어에서 핸들링 하게 된다.
생성 또는 업데이트시 by -> server데이터 로우가 생기며 추후 운영시에 운영개발자가 직접 디비를 만졌는지 또는 서버에서 디비가 핸들링되었는지 알수 있는 장점을 가지고 있다.


```java
@Configuration
@EnableJpaAuditing(auditorAwareRef = "auditorProvider")
public class JpaAuditingConfiguration {

  @Bean
  public AuditorAware<String> auditorProvider() {
    return () -> Optional.ofNullable("broker");
  }
}



@EntityListeners(AuditingEntityListener.class)
@Entity
@Table(name = "member")
public class MemberEntity {}

```


### 2. 실습및 퀴즈

아래처럼 되어있을 경우?
- memberRepository.save(memberEntity); commit시점은?

```java
1.
public void updateWithoutTransaction(Long id, AgeReq req) {

    MemberEntity memberEntity =
        memberRepository.findById(id).orElseThrow(() -> new NotFoundException("not found"));
    memberEntity.setName("hohohohoho?");

    memberRepository.save(memberEntity);

    System.out.println("dkdfldfldlf");
    memberService.updateAge(id, req);

  }


@Transactional
  public void updateWithTransaction(Long id, AgeReq req) {

    MemberEntity memberEntity =
            memberRepository.findById(id).orElseThrow(() -> new NotFoundException("not found"));
    memberEntity.setName("withwith?");

    memberRepository.save(memberEntity);

    System.out.println("dkdfldfldlf");
    memberService.updateAge(id, req);

  }

```

### 3. @Transactional의 isolation level

데이터 부정합 고려, 격리 수준이 높을수록 성능은 떨어짐

> READ_UNCOMMITED, READ_COMMITED,
REPEATABLE_READ, SERIALIZABLE


READ_COMMITED : 변경내용이 commit된 후에 다른 트랜잭션에서 조회 가능
REPEATABLE_READ : 트랜잭션 시작전 커밋된 내용에 대해서만 조회 가능


### 4. Propagation
REQUIRED, SUPPORTS, MANDATORY, REQUIRES_NEW, NOT_SUPPORTED, NEVER, NESTED


## r2dbc
R2DBC는 Reactive Relational Database Connectivity의 약어,non-blocking 지원


```java
public interface CityRepository extends Repository<City, Long> {

    Mono<City> findByNameAndStateAllIgnoringCase(String name, String state);

}
```


## querydsl

PREDICATOR를 사용해보자

https://beanbroker.github.io/2019/07/14/Java/java_querydsl_gradle4-2/

### etc
https://beanbroker.github.io/2019/08/04/Spring/spring_jpa_rollback_tip/
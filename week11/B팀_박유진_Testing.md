# 26. Testing

---
spring-boot-test   
spring-boot-test-autoconfigure   
spring-boot-starter-test는 Spring Boot Test 모듈과 Junit Jupiter, AssertJ, Hamcrest 및 기타 여러 유용한 라이브러리가 포함되어있는 라이브러리(?)이다.   

JUnit5 = JUnit Platform + JUnit Jupiter + JUnit Vintage
JUnit Platform 
 - JVM 기반 테스팅 프레임워크를 실행시키기 위한 기반 모듈
 - 플랫폼 기반으로 실행 가능한 모든 테스팅을 위해 테스트 엔진 API를 제공하며, 이러한 테스팅을 Gradle, Maven과 연동 가능하게 하는 Console Launcher를 제공한다.
 - JUnit4 환경 하에서 플랫폼 모듈을 이용하여 진행하는 테스트를 위해 Runner를 제공한다.

JUnit Jupiter
 - JUnit 5 기반 테스트 케이스를 작성하기 위한 프로그래밍 모델과 확장 모델을 지원하는 모듈
 - 주피터 기반으로 작성된 테스트 케이스를 플랫폼에서 실행시키기 위한 테스트엔진 역시 제공한다.

JUnit Vintage
 - JUnit3, JUnit4 기반 테스트를 플랫폼에서 실행시키기 위한 테스트 엔진을 제공하는 모듈이다. 

ㅊㅊ: https://awayday.github.io/2017-10-29/junit5-02/

## 26.1 범위 종속성 테스트

spring-boot-starter-test 에서 제공하는 라이브러리들 목록

 - JUnit5
 - Spring Test & Spring Boot Test
 - AssertJ
 - Hamcrest
 - Mockito
 - JSONassert
 - JsonPath

## 26.2 Spring Application Test
DI 짱짱
new Spring을 하지 않고도 객체를 인스턴스화 할 수 있고 실제 종속성 대신 모의 객체를 사용할 수도 있다.

통합테스트 필요!(Spring 사용 ApplicationContext)

Spring Framework에는 통합 테스트를 위한 전용 테스트 모듈이 포함되어 있다. 직접 org.springframework:spring-test 하거나, spring-boot-starter-test를 사용

spring-test 모듈에 대해서 잘 모르면 이 문서 읽으세오
https://docs.spring.io/spring-framework/docs/5.3.4/reference/html/testing.html#testing




# Using Spring boot

## 1. 빌드 시스템

* 의존성 관리를 지원하고 Maven Cental repository에 게시된 아티팩트를 사용하는 빌드 시스템을 선택하는 것을 권장한다.
* Maven이나 Gradle을 선택하는 것을 추천한다.
* 스프링 부트는 다른 빌드 시스템(ex: Ant)에서도 동작하지만 특별히 잘 지원되지는 않는다.


### 1.1. 의존성 관리

* 스프링 부트 릴리즈는 의존성 목록을 제공한다.
* 실제로 스프링 부트가 빌드 구성에 대한 의존성을 관리한다.
* 스프링 부트를 업그레이드 하면 의존성도 일관된 방식으로 업그레이드 된다.

* 그래도 특정 버전을 지정하고 필요에 따라 스프링 부트의 권장사항을 오버라이드할 수 있다.

* Spring Boot의 각 릴리스는 Spring Framework의 기본 버전과 연관된다. 버전을 지정하지 않는 것이 좋다.


### 1.5. Starters

* 스타터는 프로젝트를 빠르게 시작하는데 필요한 의존성을 많이 가지고 있고, transitive dependencies을 관리하는데 도움을 준다.


####  [Table 1. Spring Boot application starters](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-starter)

| 이름	| 설명 |
|------|-----|
| spring-boot-starter | 핵심 스타터, 자동 구성 지원을 포함하고, 로깅하고 YAML |
| spring-boot-starter-activemq | pache ActiveMQ를 사용하는 JMS 메시지를 위한 스타터 |
...



## 2. 코드 구성

## 2.1. default package 사용

* 클래스에 패키지 선언이 포함되지 않은 경우 default package로 간주한다.
* 일반적으로 default package는 사용하지 않는 것을 권장한다.
* @ComponentScan, @ConfigurationPropertiesScan, @EntityScan, @SpringBootApplication를 사용하면 jar의 모든 클래스를 읽어와 스프링 부트 애플리케이션에 문제가 발생할 수 있다.



### 2.2. Main Application Class의 위치

* @SpringBootApplication를 메인 함수가 있는 곳에 둔다.
* @SpringBootApplication를 사용하지 않는다면 @EnableAutoConfiguration, @ComponentScan를 대신 사용한다.

##### 일반적인 레이아웃

```java
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```


##### @SpringBootApplication 사용한 main 함수 예제

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```


## 3. Configuration Classes

* XML과 함께 SpringApplication을 사용할 수 있지만, 하나의 @Configuration 클래스를 사용하는 것을 권장한다.

### 3.1. 추가적인 Configuration Classes 가져오기

* @Configuration을 하나의 클래스에 넣을 필요는 없다.
* @Import은 추가적으로 Configuration Classes를 가져올 수 있다.
* @ComponentScan은 @Configuration를 포함하여 모든 스프링 컴포넌트를 가져온다.


### 3.2. XML 구성 가져 오기

* XML 기반읜 환경설정을 반드시 사용해야한다면 @Configuration 클래스로 시작하는 것을 권장한다.
* @ImportResource를 사용하여 XML 구성 파일을 적재할 수 있다.


## 4. Auto-configuration

* 스프링 부트 자동 환경설정은 추가한 jar 의존성을 기반으로 스프링 애플리케이션을 자동으로 구성한다.

* @EnableAutoConfiguration나 @SpringBootApplication 중 하나를 @Configuration 클래스에 추가하여 자동 환경설정을 맞춘다.


### 4.1. 자동 환경설정을 점진적으로 대체하기

* 자동 환경설정의 특정 부분을 대신하여 임의의 환결설정을 정의할 수 있다.
* 현재 적용중인 자동 환경설정을 확인해야 하는 경우 --debug 스위치를 사용하여 애플리케이션을 시작한다.


### 4.2. 특정 자동 구성 클래스 비활성화

* @SpringBootApplication를 사용하여 자동 환경구성 클래스를 배제할 수 있다.

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;

@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
public class MyApplication {
}
```

* classpath에 클래스가 없는 경우, 어노테이션의 excludeName 속성에 완전한 클래스 이름을 지정하여 사용할 수 있다.
* @EnableAutoConfiguration도 exclude, excludeName는 사용 가능하다.
* spring.autoconfigure.exclude 프로퍼티를 사용해서 자동 환경설정 클래스를 배제할 수 있다. 
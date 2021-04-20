## 1. Build Systems

- 스프링 부트는 dependency management 를 지원하는 빌드 시스템을 사용하는 것을 추천한다.
- Maven or Gradle
- 다른 빌드 툴(ex. Ant)을 사용해서 작업할 수 있지만 지원이 잘 되지 않을 수 있다.

#### Dependency Management

- Spring Boot 의 각 릴리즈 버전은 의존성 리스트를 제공한다.
- 빌드 설정에서 이들의 의존성에 대해 버전을 명시해주지 않아도 스프링 부트가 알아서 해준다.
- 스프링 부트 버전 자체를 업그레이드 하면 의존성들도 따라서 업그레이드 된다.

#### Starters

- Starter 는 빠르게 프로젝트를 셋팅하고, 실행할 수 있도록 관련된 여러 의존성들을 가지고 있다.
- 예를 들어 database access 용으로 Spring 과 JPA 를 사용한다면 spring-boot-starter-data-jpa 의존성을 추가하면 된다.
- 공식적인 starter 는 `spring-boot-starter-*` 의 형식을 띄며, * 에는 애플리케이션의 타입이 들어간다. 이러한 규칙 때문에 필요한 starter 를 찾기 쉽게 해준다.
- 우리만의 starter 를 만드는 방법도 있는데 서드 파티 스타터들의 이름은 spring-boot 로 시작할 수 없다. 스타터들의 이름은 프로젝트 이름으로 시작하는 경우가 많은데, 예를 들어 thirdpartyproject 라는 프로젝트의 스타터 이름은 `thirdpartyproject-spring-boot-starter` 로 지을 수 있겠다.

---

## 2. Structuring Your Code

스프링 부트는 어느 특정한 코드 레이아웃 같은 규격이 존재하지는 않지만,

#### Using the "default" Package

- 클래스가 package declaration 을 포함하고 있지 않으면 default package 에 있는 것으로 간주된다.
- default package 는 통상 사용을 지양하며 피하는 것이 바람직하다
- 스프링 부트 앱이 `@ComponentScan`, `@ConfigurationPropertiesScan`, `@EntityScan`,  `@SpringBootApplication` 어노테이션을 쓸 때, 모든 jar 속 모든 클래스들을 읽어야 해서 문제를 일으킬 수 있다.
- 자바에서 추천하는 패키지 네이밍 관습대로 도메인 이름을 거꾸로 나열한 패키지 명을 사용하는 것이 좋다 (ex `com.example.proejct`)

#### Locating the Main Application Class

- 일반적으로 Main Application Class 는 다른 클래스들보다 상위의 루트 패키지에 위치시키는 게 좋다
- Main Application Class? `@SpringBootApplication` 어노테이션이 붙은 클래스.
(해당 어노테이션 대신에 `@EnableAutoConfiguration`, `@ComponentScan` 을 쓰기도 한다)
- 이런 클래스들은 특정 클래스들을 찾기 위한 기본 패키지를 정의한다, 예를 들어 JPA 앱을 개발한다면, 위의 어노테이션이 붙은 클래스가 있는 패키지부터 @Entity 클래스를 찾게 될 것이다.

```
com
 +- example
     +- myapplication
         +- **Application.java**
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

---

## 3. Configuration Classes

- 스프링 부트는 자바 기반의 configuration 을 선호한다.
- xml 소스를 사용한 SpringApplication 을 사용할 수도 있지만 보통 주요한 소스는 @Configuration 클래스 파일에 두는 것을 추천한다
- 보통 main 메소드를 정의한 클래스가 주요한 @Configuration 로써의 좋은 선택지이다

### Importing Additional Configuration Classes

- 모든 Configuration 을 하나의 클래스에 집어넣을 필요는 없다
- `@Import` 어노테이션을 사용해서 추가적으로 configuration class 를
- 아니면 `@ComponentScan` 을 사용하면 `@Configuration` 클래스들을 포함해서 자동적으로 스프링 컴포넌트들을 추가할 수 있다.

### Importing XML Configuration

- 반드시... xml 방식의 설정을 사용해야만 한다면, 그래도 `@Configuration` 클래스부터 시작하는 것을 추천한다
- 그 후 `@ImportResource` 어노테이션을 이용해서 xml 설정 파일들을 읽는 게 좋다

---

## 4. Auto-configuration

- 스프링 부트는 우리가 추가한 jar dependency 를 토대로 자동 설정을 시도한다
- 예를 들어 classpath 에 `HSQLDB` 가 있다면 우리는 DB connection bean 들을 수동으로 설정할 필요가 없고, 스프링 부트가 자동으로 인메모리 DB 를 설정해 줄 것이다.
- `@EnableAutoConfiguration` 혹은 `@SpringBootApplication` 어노테이션을 `@Configuration` 클래스 파일 중 하나에 추가하면 자동 설정 된다.
- 위의 어노테이션은 오직 하나만 추가해야 한다. 보통 주요한 `@Configuration` 클래스에만 추가한다

### Gradually Replacing Auto-configuration

- 자동 설정으로 모든 것을 다 헤쳐먹을 수 없다
- 언젠가, 자동 설정중 일부를 우리만의 설정으로 교체해야 할 수도 있다.
- 예를들어 우리만의 DataSource 빈을 추가하게 되면, 자동 설정된 기본 내장 DB 지원 설정은 효력을 잃는다.
- 어떤 자동 설정이 현재 사용중인지, 이유가 무엇 때문인지 확인하려면 `—debug` 스위치를 사용하여 앱을 실행시켜 보면 된다.

### Disabling Specific Auto-configuration Classes

- 특정 자동 설정 클래스들을 사용하지 않고 싶으면 @EnableAutoConfiguration 이나 @SpringBootApplication 어노테이션의 exclude, excludeName 에 명시하면 된다.
- 해당 어노테이션이 붙은 클래스가 classpath 에 위치하지 않고 있으면 excludeName 값에 fully qualified name 을 적으면 된다.
- 혹은 `spring.autoconfigure.exclude` 프로퍼티를 사용해서 제외시킬 수 있다.

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;

@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
public class MyApplication {
}
```

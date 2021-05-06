# 코드를 구조화하기



## 1. default 패키지를 사용하면 안되는 이유

- 스프링 부트 애플리케이션은 @ComponetScan, @ConfigurationPropertiesScan, @EntityScan, @SpringBootApplication 어노테이션을 사용하여 패키지를 기준으로 Bean을 로드한다.



## 2. 메인 애플리케이션 클래스 위치시키는 곳은?

- 메인 애플리케이션 클래스를 가장 최상위 패키지에 위치시켜야 한다.
- @SpringBootApplication 어노테이션은 `main()`함수가 위치한 메인 클래스에 위치한다. 이 메인 클래스가 **패키지 검색** 의 기준이 된다.
- 예를 들어, JPA 애플리케이션을 작성하려고 한다면, @SpringBootApplication이 붙은 클래스는 `@Entity` 가 붙은 클래스를 탐색한다.
- 또한, 빈을 탐색하는 컴포넌트 스캔이 이 루트 패키지를 기준으로 실행된다.

|      | If you don’t want to use `@SpringBootApplication`, the `@EnableAutoConfiguration` and `@ComponentScan`annotations that it imports defines that behaviour so you can also use those instead. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |



예를 들어, 다음과 같은 프로젝트의 레이아웃이 있다.

```
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

`Application.java` 파일은 `main`  메서드를 구선언하였으며, @SpringBootApplication 어노테이션을 갖고 있다.

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



## 설정 클래스들

- 스프링 부트는 자바 기반의 설정을 선호한다. 
- 자바 기반 이외에도, XML 소스를 사용하여 SpringApplication을 구동할 수 있다.
- @Configuration 를 붙인 주요(primary) 설정 클래스를 추천한다.
- 보통, main() 함수가 위치한 클래스가 @Configuration을 붙이기에 알맞다.

### 부가적인 설정 클래스를 원한다면?

- 모든 @Configuration를 하나의 클래스에 붙일 필요는 없다.
- @Import 애노테이션이 부가적인 설정 클래스를 추가하는데 사용된다.
- 또는, @ComponentScan을 사용하면 스프링 컴포넌트와 @Configuration 클래스를 자동으로 가져올 수 있다.

- 만약, XML 기반의 설정을 사용해야 한다면, @Configuration 클래스에 @ImportResource 어노테이션을 붙여서 XML 기반의 설정 파일을 불러올 수 있다.



## 3. 자동설정(Auto-Configuration)

- 스프링 부트의 자동 설정은 여러분의 스프링 애플리케이션을 자동으로 설정해준다.
- 이 때 사용되는 것이 여러분이 추가한 **jar 의존** 설정이다.
- 예를 들어, HSQLDB가 classpath에 존재한다면, 스프링 부트는 자동으로 HSQLDB와 관련된 Bean들을 설정해준다.

- 자동 설정을 사용하려면, @EnableAutoConfiguration 또는 @SpringBootApplication 을 사용해야 한다.

> 참고
>
> 여러분이 하나의 스프링 부트 프로젝트를 만들 때 하나의 @SpringBootApplication이나 @EnableAutoConfiguration 만을 사용할 것이다. 따라서, 주요 @Configuration 클래스를 사용하는 것을 권장한다.



### 자동설정이 비침투적(non-invasive)인 기술인 이유

- **비침투적** 기술은 기술의 적용 사실이 코드에 직접 반영되지 않는다. 어딘가에는 기술의 적용에 따라 필요한 작업을 해줘야 하겠지만, 애플리케이션 코드 여기저기에 불쑥 등장하거나, 코드의 설계와 구현방식을 제한하지 않는다는 게 **비침투적** 기술의 특징이다.(출처: https://ee-22-joo.tistory.com/7)

- 스프링 부트의 자동 설정은 비침투적이다.

- 언제든지 여러분은 자신만의 설정을 구현하여 기존의 자동설정을 교체할 수 있다.

- 예를 들어, `DataSource` 빈을 수동으로 추가한다면, 내장 데이터베이스는 설정되지 않는다.

  

### 내 스프링 부트 프로젝트에 어떤 자동설정이 적용되었는지 확인하고 싶다면 어떻게 해야할까? 

- `--debug` 옵션과 함께 애플리케이션을 시작하면, 콘솔에 자동설정에 관한 로그가 기록된다.



### 특정 자동 설정 클래스를 무효화하려면?

- 가끔씩 특정 자동 설정을 제외하고 싶을 때가 있다.
- 이 경우, 아래와 같이 @SpringBootApplication의 파라미터에 자동설정을 제외할 설정 클래스를 추가해야 한다.

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;

@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
public class MyApplication {
}
```



- 만약 클래스패스에 `DataSourceAutoConfiguration` 클래스가 존재하지 않는다면, excludeName 파라미터에 fully qualified name(풀 패키지이름과 클래스 이름을 붙인)을 명시해주면 된다.
- 만약 @SpringBootApplication 보다, @EnableAutuoConfiguration을 사용하길 원한다면, exclude와 excludeName을 그대로 사용하면 된다.
- 3 번째 방법으로는, 스프링 부트 설정에서 제외할 자동 설정 클래스를 `spring.autoconfigure.exclude` 속성에 명시하면 된다.
- 즉, 어노테이션과 프로퍼티에서 모두 자동설정 제외를 사용할 수 있다.

- 자동 설정 클래스가 public 으로 선언되어 있지만, public API로 간주되는 자동설정의 부분은 오로지 이름 뿐이다.
- 다시 말해, 자동 설정 클래스의 컨텐츠를 직접 가져다 쓰지 말아야 한다는 것이다.


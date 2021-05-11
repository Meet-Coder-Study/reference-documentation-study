# Spring Beans and Dependency Injection
---
Spring Framework를 사용하면 Spring Bean이라는 객체를 직접 만들어 사용하는게 아니라, 의존성을 주입 받아 사용할 수 있다.
Bean을 찾기 위해서 ``@ComponentScan``, 생성자를 주입하기 위해서 ``@Autowired`` annotation을 사용한다.

## Spring IoC Container란?
IoC(Inversion of Control) : 제어의 반전, 제어의 역전을 의미한다. 기존의 모든 제어를 클라이언트 코드가 가지도록 구현하던 것에서 기존 구조적 설계와 비교해 Framework(Container)가 제어를 나누어 가져가게되어 ``의존 관계의 방향이 달라지게 되는 것을 제어가 반전, 역전`` 되었다 라고 한다.
IoC Container는 객체를 관리하고, 객체의 생성을 책임지고, 의존성을 관리하는 컨테이너이다.

## Spring Bean이란?
 - Spring에서는 POJO(Plan Old Java Object)를 ``Beans``라고 부른다
 - Beans는 Application의 핵심을 이루는 객체이며, Spring IoC(Inversion of Control) Container에 의해 생애주기가 결정된다.(인스텅스화, 관리, 생성)
 - Beans는 Configuration MetaData(XML)에 의해 생성된다.
    - Container는 이 MetaData를 통해서 Bean의 생성, Bean Life Cycle, Bean Dependency 등을 알 수 있다. 
 - Application의 객체가 지정되면, 해당 객체는 getBean() method를 통해 가져올 수 있다.

## Spring Bean 정의하는 방법
 - 일반적으로 XML 파일에 정의한다.
 - 주요 속성
    - class: 정규화된 자바 class 이름
    - id: bean의 고유 식별자
    - scope: 객체의 범위(sigleton, prototype)
    - constructor-agr: 생성 시, 생성자에 전달할 인수
    - property: 생성 시 bean setter에 전달할 인수
    - init method와 destory method
 - 예시
    ```xml
   <!-- A simple bean definition -->
    <bean id="..." class="..."></bean>

    <!-- A bean definition with scope-->
    <bean id="..." class="..." scope="singleton"></bean>

    <!-- A bean definition with property -->
    <bean id="..." class="...">
        <property name="message" value="Hello World!"/>
    </bean>

    <!-- A bean definition with initialization method -->
    <bean id="..." class="..." init-method="..."></bean>
    ```

## Spring Bean Scope
 - Spring은 기본적으로 모든 bean을 singleton으로 생성하여 관리한다.
   - application 구동 시, JVM안에서 Spring이 bean마다 하나의 객체를 생성함.
   - 그래서 Spring을 통해서 bean을 제공받으면 주입받은 bean은 동일한 객체라는 가정하에 개발 해야한다.
 - [Bean Scope](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans)
   - singleton: 하나의 Bean 정의에 대해서 Spring IoC Container 내에 단 하나의 객체만 존재한다. 기본적으로 모든 bean은 scope이 명시적으로 지정되어있지 않으면 singleton이다.
   - prototype: 하나의 Bean 정의에 대해서 다수의 객체가 존재할 수 있다. container가 사라질 때 bean이 제거되는게 아니라 gc에 의해 bean이 제거된다.
   - 이외에도 request, session, global session의 scope 단위가 존재한다.

## Dependency Injection이란?


## 예제

``RiskAssessor`` Bean을 얻기 위해 생성자 주입을 사용하는 ``@Service`` Bean 예제 
```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    @Autowired
    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...

}
```

생성자 하나인 경우는 ``@Autowired``을 사용하지 않아도 가능하다.(Spring 4.3이후부터)

```java
@Service
public class DatabaseAccountService implements AccountService {

    private final RiskAssessor riskAssessor;

    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...

}
```
> Notice how using constructor injection lets the riskAssessor field be marked as final, indicating that it cannot be subsequently changed.
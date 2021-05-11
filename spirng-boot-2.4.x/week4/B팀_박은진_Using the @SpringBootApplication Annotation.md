## **5. Spring Beans and Dependency Injection**

- 표준 Spring Framework 기술을 자유롭게 사용해 <code>Bean 정의</code>, <code>의존성 주입</code>
- <code>@ComponentScan</code>: 정의해둔 Beans 찾기
- <code>@Autosired</code>: 생성자 주입, 생성자에 의존성 주입을 받고자 하는 field를 나열하는 방법
- Application.java 클래스가 root package에 존재하면, **arg 없이도 <code>@ComponentScan</code> 추가 가능**
- application 내 모든 componenet들(<code>@Component</code>, <code>@Service</code>, <code>@Repository</code>, <code>@Controller</code> etc.)은 **자동으로 Spring Beans으로 주입**

<br>
<br>

## **6. Using the @SpringBootApplication Annotation**

- 많은 개발자들은 auto-configuration, componenet scan, application class에 추가 구성을 정의하는 것을 선호
- **<code>@SpringBootApplication</code>: 다음 3가지 기능들을 모두 활성화**

<br>

1. <code>@EnableAutoConfiguration</code>: Spring Boot의 <code>auto-configuration</code> 활성화
2. <code>@ComponenetScan</code>: app이 위치한 package에서 <code>@Component</code> scan 활성화
3. <code>@Configuration</code>: **컨텍스트에 추가 beans 등록** or **추가적인 설정 class들 import**

<br>

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

<br>

- **필요한 annotation만 직접 add 가능**

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.context.annotation.ComponentScan
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration(proxyBeanMethods = false)
@EnableAutoConfiguration
@Import({ MyConfig.class, MyAnotherConfig.class })
public class Application {

    public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
    }

}
```

<br>
<br>


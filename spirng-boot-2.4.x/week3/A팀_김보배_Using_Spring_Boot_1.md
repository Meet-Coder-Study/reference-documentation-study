# Using Spring boot

[Using Spring Boot](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-build-systems)

이번 장에서는 Build system, auto-configuration, 어떻게 어플리케이션을 실행시키는지 그리고 best practices에 대해 알아보자.

<br>

# Build Systems

## Build & Build system

- **Build**
    - 소스 코드를 컴파일, 테스트, 정적 분석 등을 실행하여 실행 가능한 애플리케이션으로 만들어주는 과정
- **Build system (tool)**
    - Build를 수행하고, 도와주는 시스템 (도구)
    - 프로젝트 생성, 테스트 빌드, 배포 등 수행
    - 외부 라이브러리 추가, 라이브러리 버전 동기화 → 기존 어려움을 해결
    - Java에선 Ant, Maven, Gradle 등이 존재

<br>

Dependency management를 지원하는 빌드 시스템을 선택하자.

- **Maven, Gradle**

    ![0](https://user-images.githubusercontent.com/37873745/115118583-d7fa6700-9fde-11eb-9be2-e8aedaf0fe8f.png)


- 다른 빌드 시스템 (Ant ..)들도 활용가능하지만, 잘 지원되지 않을 수 있다.

<br>

## Maven

[Spring Boot Maven Plugin Documentation](https://docs.spring.io/spring-boot/docs/2.4.3/maven-plugin/reference/htmlsingle/)

- Maven으로 Spring boot project를 만들면 프로젝트 최상단에 `pom.xml` 파일이 존재 → Maven 이용
    - pom ⇒ Project Object Model (POM)
    - [Maven example](https://github.com/KimDoubleB/spring-learning/blob/master/maven/pom.xml)

## Gradle

[Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.4.3/gradle-plugin/reference/htmlsingle/)

<br>


# Structuring Your Code

### "default" package

만약 `package`를 선언하지 않으면, `default package` 를 사용하는 것으로 간주한다.

- `default package` 를 사용하는 것은 Spring boot application에서 여러 어노테이션 (`@ComponentScan`, `@ConfigurationPropertiesScan`, `@EntityScan`, `@SpringBootApplication`) 사용에 있어 문제를 일으킬 수 있다.

**package를 사용할 땐, Java의 package naming convention을 따르는 것이 좋다.**

- Domain name의 반대로 package 명을 짓는 것 **(`com.example.project`)**

<br>

### Locating the Main Application Class

일반적으로 Main application class는 root package에 위치시키는 것을 추천한다.

- 보통 `@SpringBootApplication` 어노테이션은 main class에 위치한다. 이는 패키지 탐색에 있어서 암시적으로 **현재 클래스(어노테이션 된)를 base search package로 정의하는 것을 의미**한다.
- 예를 들어, JPA 어플리케이션을 개발하고 있다고 하자. 그럼, `@SpringBootApplication` 어노테이션 된 클래스가 있는 패키지에서 `@Entity` 아이템을 찾기 시작한다. 또한, Component scan도 Root package에서 수행한다.

<br>

**`@SpringBootApplication` 을 대신해, `@EnableAutoConfiguration` , `@ComponentScan` , `@Configuration` 어노테이션을 사용할 수 있다. 이 3개의 annotation을 하나로 묶어놓은 것.**

- `@Configuration` : Java-based configuration on Spring framework

- `@ComponentScan` : Enable component scanning of components you write (configurations, controllers, services and other components)

- `@EnableAutoConfiguration` : Enable auto-configuration in Spring Boot application. Automatically creates and registers beans based on both the included jar files in the classpath and the beans defined by us.

<br>

# Configuration Classes

Spring Boot에서는 Java-based configuration을 사용하는 것이 좋다. 원한다면 XML sources를 사용할 수는 있지만, Spring Boot에서는 **`@Confugration` class를 사용하는 것을 추천**한다.

<br>

## Importing Additional Configuration Classes

Configuration을 관리하기 위해 하나의 `@Configuration` 클래스에 모든 내용을 추가할 필요 없다.

- 대규모 프로젝트, 여러 기능들이 담긴 프로젝트의 경우에 기능에 맞게 또는 어느 규칙을 가지고 Configuration 파일을 나눠 관리하는 것이 개발 및 운영함에 있어 더 좋다고 하다.

Configuration을 여러 파일로 관리하기 위해선 2가지 방법을 설명하고 있다.

**1. `@Import` 를 사용한 방법: `@Import` 에 등록된 클래스들을 등록**

- 외부 패키지의 configuration (bean) 추가
- Configuration 간의 계층관계를 만들 수 있다.

**2. `@ComponentScan` 을 사용한 방법: `@Configuration` 클래스들을 자동으로 찾아 등록**

[componentScan or Import](https://github.com/KimDoubleB/spring-learning/tree/master/importOrComponentScan)

<br>

## Importing XML Configuration

물론 이전 방식처럼 XML 파일로도 관리를 할 수 있다.

- `@ImportResource` 를 사용해 XML configuration을 로드할 수 있다.

하지만 `@Configuration` class을 사용하는 것을 추천한다.

<br>
<br>

# Auto-configuration

Spring boot auto-configuration은 **사용자가 추가한 jar 의존성(dependencies)을 기반으로 Spring application을 자동으로 구성**한다.

- 단순히 `@EnableAutoConfiguration` 또는 `@SpringBootApplication` 어노테이션을 `@Configuration` class에 추가함으로써 auto-configuration 기능을 사용할 수 있다.

**`@EnableAutoConfiguration` 과 `@SpringBootApplication` 중에 하나만 사용해야만 한다.**
- Primary `@Configuration` class에 둘 중 하나만 추가해서 사용해라.

<br>


## Report Auto-configuration

만약 지금 적용되고 있는 auto-configuration 항목들을 보고 싶다면, `--debug` 옵션을 활성화 시키고 프로그램을 구동시켜보자.

- 2가지 방법을 통해 debug 옵션을 활성화 시킬 수 있다.
    - Application properties approach

        ```yaml
        # application.properties
        debug=true

        # OR application.yml
        debug: true
        ```

    - Command line approach

        ```bash
        java -jar project-0.0.1-SNAPSHOT.jar --debug
        ```

- 구동시키면 auto-configuration 적용 항목들에 대한 report가 나온다.

    ![1](https://user-images.githubusercontent.com/37873745/115118579-d3ce4980-9fde-11eb-92f7-a31ea2ecc43f.png)

<br>

## Disabling Specific Auto-configuration classes

만약 auto-configuration 되는 클래스 중 특정 클래스의 적용을 원하지 않는다면, `@SpringBootApplication` 의 **`exclude` 속성을 사용**하면 된다. (`@EnableAutoConfiguration` 도 마찬가지)

- `@SpringBootApplication` annotation

    ```java
    public @interface SpringBootApplication {

    	/**
    	 * Exclude specific auto-configuration classes such that they will never be applied.
    	 * @return the classes to exclude
    	 */
    	@AliasFor(annotation = EnableAutoConfiguration.class)
    	Class<?>[] exclude() default {};

    	/**
    	 * Exclude specific auto-configuration class names such that they will never be
    	 * applied.
    	 * @return the class names to exclude
    	 * @since 1.3.0
    	 */
    	@AliasFor(annotation = EnableAutoConfiguration.class)
    	String[] excludeName() default {};
    	...
    }
    ```

- Example

    ```java
    @SpringBootApplication(exclude = {WebMvcAutoConfiguration.class})
    public class MyApplication {
    	...
    }
    ```

    - debug report

        ![2](https://user-images.githubusercontent.com/37873745/115118582-d761d080-9fde-11eb-9792-33cf4f8d0007.png)

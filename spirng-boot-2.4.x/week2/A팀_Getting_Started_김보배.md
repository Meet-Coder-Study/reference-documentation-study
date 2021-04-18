# Getting Started

<br>

# 1. Introducing Spring Boot

Spring boot는 "stand-alone", "production-grade" 형태의 Spring 베이스 어플리케이션을 구동할 수 있도록 도와줍니다. Spring boot를 이용하면 대부분의 경우에는 적은 양의 Spring configuration 설정으로도 동작시킬 수 있습니다.

Spring Boot를 사용하면 `java -jar` 로 동작하는 Java 어플리케이션을 만들 수 있습니다. 아니면 좀 더 이전 방법인 `war` 도 가능합니다. 또한 "spring scripts"인 command line tool도 지원합니다.

핵심

- Spring boot를 이용한 대부분의 경우에는 **복잡한 Spring configuration 설정을 하지 않아도 된다**.
- `java -jar` 로 동작하는 Java 어플리케이션을 만들 수 있다. 물론, war도 지원하고 spring scripts로 커맨드 라인 상에서 작동시킬 수도 있다.
- 모든 Spring 개발에 있어 획기적으로 더 빠르고 광범위하게 액세스할 수 있는 **시작환경을 제공**한다.
- 대규모 프로젝트에 공통적으로 사용되는 **다양한 [Non-functional requirements](https://www.guru99.com/non-functional-requirement-type-example.html)을 제공**한다.
- **XML configuration 설정이 필요가 없다**.

<br>

# 2. System Requirements

현재 문서: **Spring Boot 2.4.3**

- Java: 8 ~ 15
- Spring framework: 5.3.4 이상
- build tool
    - Maven: 3.3+
    - Gradle: 6.3+

Servlet containers

- [Tomcat](http://tomcat.apache.org/tomcat-8.5-doc/) 9.0 → servlet version 4.0
- [Jetty](https://www.eclipse.org/jetty/documentation/jetty-9/index.php) 9.4 → servlet version 3.1
- [Undertow](https://undertow.io/undertow-docs/undertow-docs-2.0.0/index.html#introduction-to-undertow) 2.0 → servlet version 4.0

<br>

# 3. Installing Spring Boot

Spring Boot 구축 방법

- Classic Java development tool (Maven, Gradle)
- Command line tool

<br>

## Classic Java development

- Maven: [https://docs.spring.io/spring-boot/docs/2.4.3/maven-plugin/reference/htmlsingle/](https://docs.spring.io/spring-boot/docs/2.4.3/maven-plugin/reference/htmlsingle/)
- Gradle: [https://docs.spring.io/spring-boot/docs/2.4.3/gradle-plugin/reference/htmlsingle/](https://docs.spring.io/spring-boot/docs/2.4.3/gradle-plugin/reference/htmlsingle/)

"[Starters](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-starter)" dependencies를 제공

- A set of convenient dependency descriptors
- 도입을 원하는 기술에 대해 관련된 모든 의존 관계들을 찾아다닐 필요 없이 한번에 다 묶어놔서 매우 편리하게 사용이 가능하다.
    - 예를 들어, Spring과 JPA 사용하고 싶다면 `spring-boot-starter-data-jpa` dependency 추가만 하면 관련 의존관계가 다 추가되고, 원하는 기능들을 사용할 수 있다.
- starter는 official starter과 third party starter로 나뉜다.
    - **Official starter: `spring-boot-starter-*` 로 시작하는 dependencies**
        - `spring-boot-starter`: Core starter, auto-configuration support, logging, YAML
        - `spring-boot-starter-aop`, `spring-boot-starter-jdbc` , `spring-boot-starter-security` , `spring-boot-starter-test` , `spring-boot-starter-web` , `spring-boot-starter-webflux` , ....
    - Third party starter: 비공식 starter → [Creating Your Own Starter](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-custom-starter)
        - `spring-boot` 로 시작하는 이름으로 모듈 네임 지으면 안됨 → Official starter reserved 된 것이므로. 모듈 네임을 먼저 작성하고 뒤에 `spring-boot` 를 붙이는 식으로 이름 지음
            - ex) `foo-spring-boot-starter`, `bar-spring-boot-starter` , ...
        - 자세한 내용은 위 주소 참조

<br>


## Spring Boot CLI

[Spring Boot CLI](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-cli.html)

설치는 brew로 쉽게 가능

- `brew tap spring-io/tap`
- `brew install spring-boot`

<br>

### CLI Example

CLI tool → run Groovy scripts

```groovy
// app.groovy
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```

- `spring run app.groovy`
    - `curl localhost:8080`

![1](https://user-images.githubusercontent.com/37873745/114559549-d101ea80-9ca6-11eb-9d84-22c59d3a6105.png)

<br>

### CLI `spring init`

```bash
spring init - Initialize a new project using Spring Initializr (start.spring.io)

usage: spring init [options] [location]

Option                       Description
------                       -----------
-a, --artifactId <String>    Project coordinates; infer archive name (for
                               example 'test')
-b, --boot-version <String>  Spring Boot version (for example '1.2.0.RELEASE')
--build <String>             Build system to use (for example 'maven' or
                               'gradle') (default: maven)
-d, --dependencies <String>  Comma-separated list of dependency identifiers to
                               include in the generated project
--description <String>       Project description
-f, --force                  Force overwrite of existing files
--format <String>            Format of the generated content (for example
                               'build' for a build file, 'project' for a
                               project archive) (default: project)
-g, --groupId <String>       Project coordinates (for example 'org.test')
-j, --java-version <String>  Language level (for example '1.8')
-l, --language <String>      Programming language  (for example 'java')
--list                       List the capabilities of the service. Use it to
                               discover the dependencies and the types that are
                               available
-n, --name <String>          Project name; infer application name
-p, --packaging <String>     Project packaging (for example 'jar')
--package-name <String>      Package name
-t, --type <String>          Project type. Not normally needed if you use --
                               build and/or --format. Check the capabilities of
                               the service (--list) for more details
--target <String>            URL of the service to use (default: https://start.
                               spring.io)
-v, --version <String>       Project version (for example '0.0.1-SNAPSHOT')
-x, --extract                Extract the project archive. Inferred if a
                               location is specified without an extension

examples:

    To list all the capabilities of the service:
        $ spring init --list

    To creates a default project:
        $ spring init

    To create a web my-app.zip:
        $ spring init -d=web my-app.zip

    To create a web/data-jpa gradle project unpacked:
        $ spring init -d=web,jpa --build=gradle my-dir
```

- Test

    ![2](https://user-images.githubusercontent.com/37873745/114559545-d0695400-9ca6-11eb-9425-452e9ee1dc69.png)

    ![3](https://user-images.githubusercontent.com/37873745/114559546-d101ea80-9ca6-11eb-9196-e584cf541bd6.png)

<br>

# 4. Developing Your First Spring Boot Application

[Guides](https://spring.io/guides)

- 여기서 거의 대부분의 official starter 마다 guide 코드 제공

## Adding dependencies

- Maven, Gradle
- 이걸 다 암기하고, 배우는 것도 좋지만...
    - 이해만 하고.... , [https://start.spring.io/](https://start.spring.io/)

## Example

```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) {
        SpringApplication.run(Example.class, args);
    }

}
```

- **stereotype annotation**
    - stereotype(고정관념) + annotation(주석)
        - 사람들에겐 코드를 쉽게 읽을 수 있도록 (역할을 이해할 수 있도록)
        - Spring에겐 특정 역할을 하는 클래스라는 것을 알려준다.

    > We describe a class in our project as being a controller by using the @Controller annotation. We know just by looking at that class that it is a Controller but so does the framework which will do some cool things to this class behind the scenes.

    [Spring Stereotype Annotations](https://www.danvega.dev/blog/2017/03/27/spring-stereotype-annotations/)

    - `@Component`, `@Controller`, `@Service`, `@Repository`

<br>

- `@RequestMapping` : 라우팅 정보를 제공하는 어노테이션
    - 예제로 보자면 Spring에게 "/"로 접속할 땐, home 메서드로 들어오게 하는 것

- `@EnableAutoConfiguration` : Spring Boot에게 사용자가 Spring을 어떻게 구성하려하는지 예측하게 하는 어노테이션
    <br>

    ```java
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @AutoConfigurationPackage
    @Import(AutoConfigurationImportSelector.class)
    public @interface EnableAutoConfiguration {

    	/**
    	 * Environment property that can be used to override when auto-configuration is
    	 * enabled.
    	 */
    	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    	/**
    	 * Exclude specific auto-configuration classes such that they will never be applied.
    	 * @return the classes to exclude
    	 */
    	Class<?>[] exclude() default {};

    	/**
    	 * Exclude specific auto-configuration class names such that they will never be
    	 * applied.
    	 * @return the class names to exclude
    	 * @since 1.3.0
    	 */
    	String[] excludeName() default {};

    }
    ```

    <br>

    [EnableAutoConfiguration (Spring Boot 2.4.4 API)](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/EnableAutoConfiguration.html)

    > Enable auto-configuration of the Spring Application Context, attempting to guess and configure beans that you are likely to need.

    - 이게 어떤 뜻이야? 사용자가 추가한 의존성(jar dependencies)을 바탕으로 사용자가 필요로 할 것 같은 bean들을 생성한다.
        - 예를 들어, 사용자가 `tomcat-embeded.jar` 를 classpath에 추가해놨다면, 사용자가 `TomcatServletWebServerFactory` 를 사용할 것을 예측하고 bean으로 등록한다.
    - META-INF 디렉토리 - spring.factories에 자동으로 가져올 bean들이 정의 되어있다.

<br>

## Creating an executable jar

- Executable jar는 컴파일된 클래스와 작성한 코드들이 작동되는데 필요한 의존성 jar들을 포함하는 아카이브 (패키징)

    > Executable jars(sometimes called "fat jars") are archives containing your compiled classes along with all of the jar dependencies that your code needs to run.

Boot 2.x 이상(Gradle 4.0 or later) 버전에서는 `bootJar` 와 `bootWar` 를 통해 application을 패키징할 수 있다.

```bash
./gradlew bootJar

# If you want to make war
./gradlew bootWar
```
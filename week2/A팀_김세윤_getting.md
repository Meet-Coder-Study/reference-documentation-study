## Spring Boot란?

- 독립적인 프로덕션의 Spring 기반 애플리케이션을 사용할 수 있습니다.
- 최소한으로 시작할 수 있도록 Spring과 서드 파티 라이브러리를 제공합니다.
- Spring Boot를 사용하면 Spring configuration이 거의 필요하지 ㅇ낳습니다.

### 목표

- Spring을 이용한 개발에 대해 빠르고, 넓게 접근할 수 있는 시작 환경을 제공합니다.
- 대규모 비기능적 요구사항(예를들어 입베디드 서버, 보안, 메트릭, 상태 확인 및 외부 구성)등에 대해 제공합니다.
- XML 구성이 필요 없습니다.

### 현재 제공하고 있는 환경

- 2.4.4 기준입니다.
- Java8 ~ Java 16 까지 호환됩니다.
- Spring Framework 5.3.5 이상이 필요합니다.
- 빌드 도구로는 Maven 3.3 이상 / Gradle 6.3 이상을 제공해야 합니다.
- Tomcate 9.0(Servlet 4.0) / Jetty 9.4(Servlet 3.1) / Undertow 2.0 (Servlet 4.0)의 Servlet Container 버전이 필요합니다.

## 스프링 부트 시작하기

- 위에서 말씀드렸듯이 Java8 이상이 필요합니다. (`java -v` 을 이용해 버전을 확인해보시면 좋습니다.)

## 빌드 환경

### Maven Installation

- 아래와 같이 Maven을 설치 할 수 있습니다.

    ```jsx
    brew install maven // Mac OS
    sudo apt-get install maven // Ubuntu
    choco install maven // Window
    ```

```jsx
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<!-- ... -->
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

- 기본적으로 `<groupId>org.springframework.boot</groupId>` 으로 의존성을 확인한다.
- `spring-boot-starter-parent` 를 상속하려면 아래와 같이 작성하면 됩니다.

```jsx
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.4.4</version>
</parent>
```

- starter들의 목록을 보고 싶다면 [여기](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)에서 확인이 가능합니다.

### Gradle Installation

```jsx
plugins {
	id 'org.springframework.boot' version '2.4.4'
}

apply plugin: 'java'
apply plugin: 'io.spring.dependency-management'

dependencies {
	implementation('org.springframework.boot:spring-boot-starter-web')
	implementation('org.springframework.boot:spring-boot-starter-data-jpa')
}
```

- plugin을 통해서 spring boot 버전을 설정해 dependency-management로 해당 의존성들의 버전을 관리합니다.

### Gradle Wrapper

- 프로젝트 빌드를 위해 Gradle을 설치해야 하는데, Gradle을 로컬에 굳이 설치 하지 않아도, 빌드 프로세스를 위해 스크립트 및 라이브러리 입니다.

![image](https://user-images.githubusercontent.com/53366407/114699041-bee18400-9d5a-11eb-8aa1-9d52e7e402eb.png)

- 아래와 같은 이점을 얻을 수 있습니다
    - 특정 Gradle 버전에서 프로젝트를 표준화해서 안정적으로 빌드할 수 있게 합니다.
    - 새 Gradle 버전을 다른 사용자 및 실행 환경에 쉽게 정의 할수 잇습니다.
- 작동하는 방법
    1. 새 Gradle 프로젝트를 설정하고 여기에 Wrapper를 추가합니다.
    2. 이미 제공 하는 Wrapper로 프로젝트를 실행합니다.
    3. Wrapper를 Gradle 새 버전으로 업데이트 합니다.

## Migration

- Spring Boot 1.x 버전에서 2.x 버전으로 Migration 방법은 [이 링크](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)에 있습니다.
- [릴리즈 노트](https://github.com/spring-projects/spring-boot/wiki)도 매번 확인해주시면 좋습니다.
- `spring-boot-properties-migrator` 모듈을 지원하여 업데이트 되었을때 기능이 제거되거나 변경되는 사항을 진단해줍니다.
- 주의) 마이그레이션이 끝나면 예전 의존성을 삭제해주셔야 합니다

## Developing Your First Spring Boot Application

- 공식문서에서 Maven으로 설정되어 있어서 Maven으로 시작해보도록 하겠습니다.
- spring 프로젝트를 만들기 전에 `java -version`과 `man -v`을 이용해 java와 maven이 설치되어 있는지 확인해야 합니다.
- [Spring Initalizr](https://start.spring.io/)를 사용하면 쉽게 스프링을 만들수 있습니다.
- 생성하게 되면 `pom.xml` 이 생성되어있을 겁니다. 아래와 같이 말이죠.

```jsx
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.4</version>
    </parent>

    <description/>
    <developers>
        <developer/>
    </developers>
    <licenses>
        <license/>
    </licenses>
    <scm>
        <url/>
    </scm>
    <url/>

    <!-- Additional lines to be added here... -->

</project>
```

- `mvn dependency:tree` 을 사용하면 쉽게 jar 파일을 만들 수 있습니다.
- `spring-boot-starter-parent` 이 의존성은 그자체로는 의존성을 가지고 있지 않습니다. 간단하게 web의존성을 사용하려면 아래를 추가하시면 됩니다.

```jsx
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies> 
```

- 다시 `mvn dependency:tree` 를 하면 web 의존성이 추가되는걸 확인할 수 있습니다.
- 우리는 아주 간단한 `/` 로 접속하면 "Hello world"가 나오도록 해볼 겁니다. 이때 아래와 같이 코드를 작성하면 되는데, 이 코드는 `src/main/java/Example.java` 여기에 작성할 겁니다.

```jsx
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

- @RestController
    - MVC패턴에서 C를 의미하는 것으로 요청이 들어오면 핸들링하는 역활을 담당합니다.
    - @Controller와 @ResponseBody가 결합된 방식입니다.
- @EnableAutoConfiguration
    - spring.factories 안에 들어있는 수많은 자동 설정들이 조건에 따라 적용이 되어 수 많은 Bean들이 생성되고, 스프링 부트 어플리케이션이 실행한다.
- @RequestMapping
    - 라우팅 기능을 제공해주며, `/` 이 주소는 기본적으로 home 메서드로 매핑이 됩니다.
    - 즉, Spring이 결과를 home 메서드의 결과로 클라이언트에게 응답을 보낸다는 의미입니다.
- "main" Method
    - SpringApplication.run은 SpringAppliction을 bootstrap해 Spring을 시작하고 자동 구성된 Tomcat 웹서버를 시작한다.

### Running the Example

- `mvn spring-boot:run` 을 이용해 실행하면 기본적으로 `[localhost:8080](http://localhost:8080)` 으로 접속하면 Hello World가 출력되는걸 볼수 있다.
- ctrl + c 를 클릭하면 종료된다.

### Creating an Executable Jar

- 배포 가능한 jar 파일을 생성할 수 있습니다.
- 모든 종속성과 함께 컴파일 된 클래스를 포함하는 곳인데, <dependencies> 아래에 다음과 같이 추가하면 됩니다.

```jsx
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

- 빌드 도구를 메이븐을 사용한다는 것이고 `mvn package` 명령어를 실행하면 jar 파일을 만들 수 있습니다.

## 참고자료

[Getting Started](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html)

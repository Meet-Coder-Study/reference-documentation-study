# Getting Started

## 1. Spring Boot 소개

* 스프링부트는 스프링 기반 애플리케이션을 생성하는데 도움을 준다.
* 스프링 플랫폼과 third-party 라이브러리를 쉽게 사용할 수 있다.
  
* 스프링 부트는 java -jar 나 war 파일 배포 방식(Maven, Gradle) 또는 스프링 스크립트 툴로 자바 애플리케이션을 생성한다.

* 주요 목표:
  * Spring 개발에 대해 빠르고 광범위하게 접근할 수 있는 시작 환경을 제공한다. 
  * 프로젝트에 비기능적 기능을 제공한다. (예 : 임베디드 서버, 보안, 메트릭, 상태 확인 및 외부 구성)
  * 코드 생성이 전혀없고 XML 구성이 필요하지 않다.



## 2. 시스템 요구사항
* Spring Boot 2.4.3 버전 기준
  * Java 8, Java 15 이상
  * Spring Framework 5.3.4 이상

* 빌드 도구

| 빌드 도구 | 버전 |
|---------|-----|
| Maven		| 3.3+ |
| Gradle  | 6 (6.3이상). 5.6.x 도 지원하지만 deprecated |


### 2.1. Servlet Containers

| 서블릿 컨테이너   | 서블릿 버전 |
|---------------|----------|
| Tomcat 9.0    | 4.0		   |
| Jetty 9.4     | 3.1      |
| Undertow 2.0  | 4.0      |

이외에도 Spring Boot Application은 Servlet 3.1 이상 지원되는 서블릿 컨테이너면 배포할 수 있다.



# 3. Spring Boot 설치

* Java SDK v1.8 이상 필요

```cmd
# 자바 버전 확인
$ java -version
```


## 3.1. 자바 개발자를 위한 설치 지시

* 스프링 부트는 다른 standard Java library와 동일한 방식으로 사용할 수 있다.
* classpath에 spring-boot-*.jar 파일을 경로를 설정한다.
* 스프링부트는 특별한 통합 도구가 필요하지 않다. 어떤 IDE나 텍스트 편집을 사용할 수 있다.
* 스프링 부트 애플리케이션을 다른 자바 프로그램처럼 실행하고 디버깅할 수 있다.

* Spring Boot jar 파일을 복사해서 사용할 수 있지만, 보통은 Maven이나 Gradle 같은 의존성 관리를 지원하는 빌드 툴을 사용하는 것을 권장한다.



#### 3.1.1. Maven 설치

* 스프링 부트는 Apache Maven 3.3 이상 버전을 사용해야 한다.
* 스프링 부트의 의존성은 org.springframework.boot라는 groupId를 사용한다.



#### 3.1.2. Gradle 설치

* 스프링 부트는 Gradle 6 (6.3 이상), Gradle 5.6.x는 지원하지만 deprecated
* Spring Boot 의존성은 org.springframework.boot라는 group를 사용한다.



## 3.2. Spring Boot CLI 이용한 설치

* Spring Boot CLI (Command Line Interface)는 스프링 프로토타입을 빠르게 만들 수 있는 명령 줄 도구다.
* Groovy 스크립트로 실행한다.



### 3.2.1. 설치 매뉴얼

* 아래의 파일을 다운로드 받는다.
  * spring-boot-cli-2.4.3-bin.zip
	* spring-boot-cli-2.4.3-bin.tar.gz

* 설치받은 파일에서 INSTALL.txt 의 지시에 따라 설치한다.




### 3.2.2. SDKMAN을 사용한 설치

#### SDKMAN 설치

```cmd
$ curl -s "https://get.sdkman.io" | bash

$ source "$HOME/.sdkman/bin/sdkman-init.sh"

$ sdk version
SDKMAN 5.11.0+644

```

#### SDKMAN을 이용한 설치

```cmd
$ sdk install springboot
$ spring --version
Spring Boot v2.4.3
```

* CLI 버전 조회

```cmd
$ sdk ls springboot

================================================================================
Available Springboot Versions
================================================================================
 > * 2.4.4               2.1.15.RELEASE      1.5.18.RELEASE      1.3.4.RELEASE  
     2.4.3               2.1.14.RELEASE      1.5.17.RELEASE      1.3.3.RELEASE  
     2.4.2               2.1.13.RELEASE      1.5.16.RELEASE      1.3.2.RELEASE  
....

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```



### 3.3. 이전 버전의 스프링 부트를 업그레이드하기

* 스프링 부트 1.x 릴리즈를 업그레이드하려면 프로젝트 위키에서 “migration guide”를 확인하라.
* 릴리즈 노트에서 새롭고 주목할만한 기능(New and Noteworthy) 목록 확인해라.

https://github.com/spring-projects/spring-boot/wiki#release-notes

* 새 기능 릴리즈로 업그레이드 할 때 일부 프로퍼티는 이름이 바뀌거나 삭제될 수 있다.
* 스프링 부트는 애플리케이션 환경을 분석하는 방법을 제공하고 startup을 진단하여 표시한다. 또 런타임시 일시적으로 프로터피를 마이그레이션 할 수 있다.
* 이 기능을 사용하려면 프로젝트에 아래의 속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-properties-migrator</artifactId>
    <scope>runtime</scope>
</dependency>
```
Properties that are added late to the environment, such as when using @PropertySource, will not be taken into account.
Once you’re done with the migration, please make sure to remove this module from your project’s dependencies.
To upgrade an existing CLI installation, use the appropriate package manager command (for example, brew upgrade). If you manually installed the CLI, follow the standard instructions, remembering to update your PATH environment variable to remove any older references.

1. Developing Your First Spring Boot Application
This section describes how to develop a small “Hello World!” web application that highlights some of Spring Boot’s key features. We use Maven to build this project, since most IDEs support it.

The spring.io web site contains many “Getting Started” guides that use Spring Boot. If you need to solve a specific problem, check there first.

You can shortcut the steps below by going to start.spring.io and choosing the "Web" starter from the dependencies searcher. Doing so generates a new project structure so that you can start coding right away. Check the Spring Initializr documentation for more details.

Before we begin, open a terminal and run the following commands to ensure that you have valid versions of Java and Maven installed:

$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
$ mvn -v
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-17T14:33:14-04:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
This sample needs to be created in its own directory. Subsequent instructions assume that you have created a suitable directory and that it is your current directory.


## 4. 첫번째 스프링 부트 애플리케이션 개발 

### 4.1. POM 파일 생성

* Maven pom.xml 파일을 생성한다.

```xml
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
        <version>2.4.3</version>
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




### 4.2. Classpath 의존성을 추가한다.

* Spring Boot는 클래스 경로에 jar를 추가 할 수있는 여러 "스타터"를 제공한다.
* POM의 상위 섹션에서 spring-boot-starter-parent 를 추가한다.


```cmd
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```

* mvn dependency:tree 명령은 프로젝트의 의존성을 트리로 표시한다.
* pom.xml 파일에 spring-boot-starter-web dependency 를 추가하고 mvn dependency:tree를 실행하면 추가적으로 의존성을 확인할 수 있다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

```cmd
$ mvn dependency:tree
...
[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
[INFO] \- org.springframework.boot:spring-boot-starter-web:jar:2.4.3:compile
[INFO]    +- org.springframework.boot:spring-boot-starter:jar:2.4.3:compile
[INFO]    |  +- org.springframework.boot:spring-boot:jar:2.4.3:compile
[INFO]    |  +- org.springframework.boot:spring-boot-autoconfigure:jar:2.4.3:compile
[INFO]    |  +- org.springframework.boot:spring-boot-starter-logging:jar:2.4.3:compile
[INFO]    |  |  +- ch.qos.logback:logback-classic:jar:1.2.3:compile
[INFO]    |  |  |  +- ch.qos.logback:logback-core:jar:1.2.3:compile
[INFO]    |  |  |  \- org.slf4j:slf4j-api:jar:1.7.30:compile
[INFO]    |  |  +- org.apache.logging.log4j:log4j-to-slf4j:jar:2.13.3:compile
[INFO]    |  |  |  \- org.apache.logging.log4j:log4j-api:jar:2.13.3:compile
[INFO]    |  |  \- org.slf4j:jul-to-slf4j:jar:1.7.30:compile
[INFO]    |  +- jakarta.annotation:jakarta.annotation-api:jar:1.3.5:compile
[INFO]    |  +- org.springframework:spring-core:jar:5.3.4:compile
[INFO]    |  |  \- org.springframework:spring-jcl:jar:5.3.4:compile
[INFO]    |  \- org.yaml:snakeyaml:jar:1.27:compile
[INFO]    +- org.springframework.boot:spring-boot-starter-json:jar:2.4.3:compile
[INFO]    |  +- com.fasterxml.jackson.core:jackson-databind:jar:2.11.4:compile
[INFO]    |  |  +- com.fasterxml.jackson.core:jackson-annotations:jar:2.11.4:compile
[INFO]    |  |  \- com.fasterxml.jackson.core:jackson-core:jar:2.11.4:compile
[INFO]    |  +- com.fasterxml.jackson.datatype:jackson-datatype-jdk8:jar:2.11.4:compile
[INFO]    |  +- com.fasterxml.jackson.datatype:jackson-datatype-jsr310:jar:2.11.4:compile
[INFO]    |  \- com.fasterxml.jackson.module:jackson-module-parameter-names:jar:2.11.4:compile
[INFO]    +- org.springframework.boot:spring-boot-starter-tomcat:jar:2.4.3:compile
[INFO]    |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:9.0.43:compile
[INFO]    |  +- org.glassfish:jakarta.el:jar:3.0.3:compile
[INFO]    |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:9.0.43:compile
[INFO]    +- org.springframework:spring-web:jar:5.3.4:compile
[INFO]    |  \- org.springframework:spring-beans:jar:5.3.4:compile
[INFO]    \- org.springframework:spring-webmvc:jar:5.3.4:compile
[INFO]       +- org.springframework:spring-aop:jar:5.3.4:compile
[INFO]       +- org.springframework:spring-context:jar:5.3.4:compile
[INFO]       \- org.springframework:spring-expression:jar:5.3.4:compile
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  16.909 s
[INFO] Finished at: 2021-04-13T22:24:01+09:00
[INFO] ------------------------------------------------------------------------
```


### 4.3. 코드 작성

* 기본적으로 Maven은 src/main/java 의 소스를 컴파일한다.
* src/main/java/Example.java 를 만들어보자.

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


#### 4.3.1. @RestController and @RequestMapping Annotations

* @Controller
  * 웹 요청을 처리할 때 고려
 
* @RequestMapping
  * 라우팅 정보를 제공한다.

* @RestController
  * 문자열을 호출자에게 직접 렌더링하도록 Spring에 지시합니다.

* @RestController 와 @RequestMapping은 Spring MVC 어노테이션이다. 



#### 4.3.2. @EnableAutoConfiguration Annotation

* @EnableAutoConfiguration
  * 추가 한 jar 종속성에 따라 Spring을 구성하는 방법을 "추측"하도록 Spring Boot에 지시합니다. 
  * spring-boot-starter-web은 Tomcat과 Spring MVC를 추가했기 때문에 auto-configuration은 웹 애플리케이션을 개발 중이라고 가정하고 그에 따라 Spring을 설정한다.




#### 4.3.3. main 함수

* main 함수는 자바 애플리케이션의 진입점이다. 여기서 SpringApplication 클래스에 위임한다.



### 4.4. 실행 예제

```cmd
$ mvn spring-boot:run

[INFO] Attaching agents: []

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.4.3)

2021-04-13 22:47:34.403  INFO 21249 --- [           main] Example                                  : Starting Example using Java 15.0.2 on kimhaegyeong with PID 21249 (/Users/schema9/programming/meetcoder/maven/target/classes started by schema9 in /Users/schema9/programming/meetcoder/maven)
2021-04-13 22:47:34.405  INFO 21249 --- [           main] Example                                  : No active profile set, falling back to default profiles: default
2021-04-13 22:47:35.356  INFO 21249 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-04-13 22:47:35.372  INFO 21249 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-04-13 22:47:35.372  INFO 21249 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.43]
2021-04-13 22:47:35.466  INFO 21249 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-04-13 22:47:35.466  INFO 21249 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 1024 ms
2021-04-13 22:47:35.660  INFO 21249 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-04-13 22:47:35.844  INFO 21249 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-04-13 22:47:35.852  INFO 21249 --- [           main] Example                                  : Started Example in 1.869 seconds (JVM running for 2.221)
```

* 브라우저에서 localhost:8080 를 실행하면 확인할 수 있다.
```cmd
Hello World!
```

* ctrl-c 눌러 애플리케이션을 종료한다.



### 4.5. 실행가능한 Jar 생성

* 실행가능한 jar 파일을 생성하기 위해서는 pom.xml 에 pring-boot-maven-plugin를 추가해라

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

```cmd
$ mvn package

[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< com.example:myproject >------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:3.2.0:resources (default-resources) @ myproject ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Using 'UTF-8' encoding to copy filtered properties files.
[INFO] skip non existing resourceDirectory /Users/schema9/progra
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.363 s
[INFO] Finished at: 2021-04-13T22:56:08+09:00
[INFO] ------------------------------------------------------------------------
```



* target 디렉터리에 myproject-0.0.1-SNAPSHOT.jar 가 생성된다.

```cmd
$ ll ./target 
total 8
drwxr-xr-x  3 schema9  staff    96B  4 13 22:47 classes
drwxr-xr-x  3 schema9  staff    96B  4 13 22:47 generated-sources
drwxr-xr-x  3 schema9  staff    96B  4 13 22:55 maven-archiver
drwxr-xr-x  3 schema9  staff    96B  4 13 22:47 maven-status
-rw-r--r--  1 schema9  staff   2.1K  4 13 22:55 myproject-0.0.1-SNAPSHOT.jar
```

* jar tvf를 사용해서 jar 파일 내부 확인

```cmd
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
   154 Tue Apr 13 22:55:28 KST 2021 META-INF/MANIFEST.MF
     0 Tue Apr 13 22:55:28 KST 2021 META-INF/
     0 Tue Apr 13 22:55:28 KST 2021 META-INF/maven/
     0 Tue Apr 13 22:55:28 KST 2021 META-INF/maven/com.example/
     0 Tue Apr 13 22:55:28 KST 2021 META-INF/maven/com.example/myproject/
   938 Tue Apr 13 22:47:24 KST 2021 Example.class
   983 Tue Apr 13 22:23:42 KST 2021 META-INF/maven/com.example/myproject/pom.xml
    64 Tue Apr 13 22:55:28 KST 2021 META-INF/maven/com.example/myproject/pom.properties
```

* jar 파일 실행

```cmd
java -jar ./target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.4.3)

2021-04-13 23:01:19.287  INFO 21630 --- [           main] Example                                  : Starting Example using Java 1.8.0_144 on kimhaegyeong with PID 21630 (/Users/schema9/programming/meetcoder/maven/target/myproject-0.0.1-SNAPSHOT.jar started by schema9 in /Users/schema9/programming/meetcoder/maven)
```

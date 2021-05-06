# 3. Using Spring Boot (2 / 2)

## 5. 스프링 빈과 의존성 주입

You are free to use any of the standard Spring Framework techniques to define your beans and their injected dependencies. We often find that using `@ComponentScan` (to find your beans) and using `@Autowired` (to do constructor injection) works well.

If you structure your code as suggested above (locating your application class in a root package), you can add `@ComponentScan` without any arguments. All of your application components (`@Component`, `@Service`, `@Repository`, `@Controller` etc.) are automatically registered as Spring Beans.

The following example shows a `@Service` Bean that uses constructor injection to obtain a required `RiskAssessor` bean:

표준 Spring Framework 기술을 자유롭게 사용하여 Bean과 주입 된 종속성을 정의 할 수 있습니다. 종종`@ComponentScan` (bean을 찾기 위한)을 사용하고`@Autowired` (생성자 주입을 수행하기 위한)를 사용하는 것이 잘 작동한다는 것을 종종 발견합니다.

위에 제안 된대로 코드를 구성하는 경우 (루트 패키지에 애플리케이션 클래스가 있는 상태) 인수없이 `@ComponentScan`을 추가 할 수 있습니다. 모든 애플리케이션 구성 요소 (`@Component`,`@Service`,`@Repository`,`@Controller` 등)는 자동으로 Spring Bean으로 등록됩니다.

다음 예는 생성자 주입을 사용하여 필수 `RiskAssessor` Bean을 가져 오는 `@Service` Bean을 보여줍니다.

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

If a bean has one constructor, you can omit the @Autowired, as shown in the following example:

Bean에 하나의 생성자가있는 경우 다음 예제와 같이 @Autowired를 생략 할 수 있습니다.

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

## 6. @SpringBootApplication 어노테이션

Many Spring Boot developers like their apps to use auto-configuration, component scan and be able to define extra configuration on their "application class". A single @SpringBootApplication annotation can be used to enable those three features, that is:

- `@EnableAutoConfiguration`: enable [Spring Boot’s auto-configuration mechanism](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-auto-configuration)
- `@ComponentScan`: enable `@Component` scan on the package where the application is located (see [the best practices](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-structuring-your-code))
- `@Configuration`: allow to register extra beans in the context or import additional configuration classes

많은 Spring Boot 개발자는 자동 구성, 구성 요소 스캔을 사용하고 "애플리케이션 클래스"에 추가 구성을 정의 할 수있는 앱을 좋아합니다. 단일 @SpringBootApplication 주석을 사용하여 이러한 세 가지 기능을 활성화 할 수 있습니다.

- `@EnableAutoConfiguration`: Spring Boot의 자동 구성 메커니즘 사용
- `@ComponentScan`: 애플리케이션이있는 패키지에서 `@Component` 스캔을 사용 설정합니다 (권장 사항 참조).
- `@Configuration`: 컨텍스트에 추가 빈을 등록하거나 추가 구성 클래스를 가져올 수 있습니다.

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

## 7. 어플리케이션 실행하기

One of the biggest advantages of packaging your application as a jar and using an embedded HTTP server is that you can run your application as you would any other. The sample applies to debugging Spring Boot applications. You do not need any special IDE plugins or extensions.

애플리케이션을 jar로 패키징하고 임베디드 HTTP 서버를 사용할 때의 가장 큰 장점 중 하나는 다른 애플리케이션과 마찬가지로 애플리케이션을 실행할 수 있다는 것입니다. 샘플은 Spring Boot 애플리케이션 디버깅에 적용됩니다. 특별한 IDE 플러그인이나 확장이 필요하지 않습니다.

### 7.1. Running from an IDE

You can run a Spring Boot application from your IDE as a Java application. However, you first need to import your project. Import steps vary depending on your IDE and build system. Most IDEs can import Maven projects directly. For example, Eclipse users can select `Import…` → `Existing Maven Projects` from the `File` menu.

If you cannot directly import your project into your IDE, you may be able to generate IDE metadata by using a build plugin. Maven includes plugins for [Eclipse](https://maven.apache.org/plugins/maven-eclipse-plugin/) and [IDEA](https://maven.apache.org/plugins/maven-idea-plugin/). Gradle offers plugins for [various IDEs](https://docs.gradle.org/current/userguide/userguide.html).

IDE에서 Java 애플리케이션으로 Spring Boot 애플리케이션을 실행할 수 있습니다. 그러나 먼저 프로젝트를 가져와야합니다. 가져 오기 단계는 IDE 및 빌드 시스템에 따라 다릅니다. 대부분의 IDE는 Maven 프로젝트를 직접 가져올 수 있습니다. 예를 들어 Eclipse 사용자는 `file` 메뉴에서 `Import…` → `Existing Maven Projects`를 선택할 수 있습니다.

프로젝트를 IDE로 직접 가져올 수없는 경우 빌드 플러그인을 사용하여 IDE 메타 데이터를 생성 할 수 있습니다. Maven에는 Eclipse 및 IDEA 용 플러그인이 포함되어 있습니다. Gradle은 다양한 IDE 용 플러그인을 제공합니다.

### 7.2. Running as a Packaged Application

If you use the Spring Boot Maven or Gradle plugins to create an executable jar, you can run your application using `java -jar`, as shown in the following example:

Spring Boot Maven 또는 Gradle 플러그인을 사용하여 실행 가능한 jar를 만드는 경우 다음 예와 같이 java -jar를 사용하여 애플리케이션을 실행할 수 있습니다.

```bash
$ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
```

It is also possible to run a packaged application with remote debugging support enabled. Doing so lets you attach a debugger to your packaged application, as shown in the following example:

원격 디버깅 지원이 활성화 된 상태에서 패키지화 된 응용 프로그램을 실행할 수도 있습니다. 이렇게하면 다음 예제와 같이 패키징 된 애플리케이션에 디버거를 연결할 수 있습니다.

```bash
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/myapplication-0.0.1-SNAPSHOT.jar
```

### 7.3. Using the Maven Plugin

The Spring Boot Maven plugin includes a run goal that can be used to quickly compile and run your application. Applications run in an exploded form, as they do in your IDE. The following example shows a typical Maven command to run a Spring Boot application:

```bash
$ mvn spring-boot:run
```

### 7.4. Using the Gradle Plugin

```bash
$ gradle bootRun
```

### 7.5. Hot Swapping

Since Spring Boot applications are plain Java applications, JVM hot-swapping should work out of the box. JVM hot swapping is somewhat limited with the bytecode that it can replace. For a more complete solution, JRebel can be used.

Spring Boot 애플리케이션은 일반 Java 애플리케이션이므로 JVM 핫스왑은 즉시 작동해야합니다. JVM 핫 스와핑은 대체 할 수있는 바이트 코드로 인해 다소 제한됩니다. 보다 완전한 솔루션을 위해 JRebel을 사용할 수 있습니다.

The spring-boot-devtools module also includes support for quick application restarts. See the Developer Tools section later in this chapter and the Hot swapping “How-to” for details.

spring-boot-devtools 모듈은 또한 빠른 애플리케이션 재시작에 대한 지원을 포함합니다. 자세한 내용은이 장 뒷부분의 개발자 도구 섹션과 핫 스와핑 "방법"을 참조하십시오.

## 8. Developer Tools

Spring Boot includes an additional set of tools that can make the application development experience a little more pleasant. The spring-boot-devtools module can be included in any project to provide additional development-time features. To include devtools support, add the module dependency to your build, as shown in the following listings for Maven and Gradle:

Spring Boot에는 애플리케이션 개발 경험을 좀 더 즐겁게 만들 수있는 추가 도구 세트가 포함되어 있습니다. spring-boot-devtools 모듈은 추가 개발 시간 기능을 제공하기 위해 모든 프로젝트에 포함될 수 있습니다. devtools 지원을 포함하려면 Maven 및 Gradle에 대한 다음 목록에 표시된대로 빌드에 모듈 종속성을 추가합니다.

Maven

```bash
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

gradle

```bash
dependencies {
    developmentOnly("org.springframework.boot:spring-boot-devtools")
}
```

Developer tools are automatically disabled when running a fully packaged application. If your application is launched from java -jar or if it is started from a special classloader, then it is considered a “production application”. You can control this behavior by using the spring.devtools.restart.enabled system property. To enable devtools, irrespective of the classloader used to launch your application, set the -Dspring.devtools.restart.enabled=true system property. This must not be done in a production environment where running devtools is a security risk. To disable devtools, exclude the dependency or set the -Dspring.devtools.restart.enabled=false system property.

완전히 패키징 된 애플리케이션을 실행하면 개발자 도구가 자동으로 비활성화됩니다. 애플리케이션이 java -jar에서 시작되거나 특수 클래스 로더에서 시작된 경우 "프로덕션 애플리케이션"으로 간주됩니다. spring.devtools.restart.enabled 시스템 속성을 사용하여이 동작을 제어 할 수 있습니다. 애플리케이션을 시작하는 데 사용 된 클래스 로더에 관계없이 devtools를 활성화하려면 -Dspring.devtools.restart.enabled = true 시스템 속성을 설정합니다. devtools를 실행하는 것이 보안 위험이있는 프로덕션 환경에서는이 작업을 수행해서는 안됩니다. devtools를 비활성화하려면 종속성을 제외하거나 -Dspring.devtools.restart.enabled = false 시스템 속성을 설정합니다.

Flagging the dependency as optional in Maven or using the developmentOnly configuration in Gradle (as shown above) prevents devtools from being transitively applied to other modules that use your project.

Maven에서 종속성을 선택 사항으로 플래그 지정하거나 Gradle에서 developmentOnly 구성을 사용하면(위의 그림 참조) 프로젝트를 사용하는 다른 모듈에 devtools가 전이적으로 적용되는 것을 방지할 수 있습니다.

### 8.1. Property Defaults

Several of the libraries supported by Spring Boot use caches to improve performance. For example, template engines cache compiled templates to avoid repeatedly parsing template files. Also, Spring MVC can add HTTP caching headers to responses when serving static resources.

Spring Boot에서 지원하는 여러 라이브러리는 캐시를 사용하여 성능을 향상시킵니다. 예를 들어 템플릿 엔진은 컴파일된 템플릿을 캐시하여 템플릿 파일을 반복적으로 구문 분석하지 않도록 합니다. 또한 Spring MVC는 정적 리소스를 제공할 때 HTTP 캐싱 헤더를 응답에 추가할 수 있습니다.

While caching is very beneficial in production, it can be counter-productive during development, preventing you from seeing the changes you just made in your application. For this reason, spring-boot-devtools disables the caching options by default.

캐싱은 프로덕션에서 매우 유용하지만 개발 중에 역효과를 낼 수 있으므로 애플리케이션에서 방금 변경한 내용을 볼 수 없습니다. 이러한 이유로 spring-boot-devtools는 기본적으로 캐싱 옵션을 실행 중지합니다.

Cache options are usually configured by settings in your application.properties file. For example, Thymeleaf offers the spring.thymeleaf.cache property. Rather than needing to set these properties manually, the spring-boot-devtools module automatically applies sensible development-time configuration.

캐시 옵션은 일반적으로 application.properties 파일의 설정으로 구성됩니다. 예를 들어, Thymeleaf는 봄을 제공합니다.tymeleaf.cache 속성입니다. 이러한 속성을 수동으로 설정할 필요 없이 스프링 부트-개발 도구 모듈은 자동으로 합리적인 개발 시간 구성을 적용합니다.

Because you need more information about web requests while developing Spring MVC and Spring WebFlux applications, developer tools will enable DEBUG logging for the web logging group. This will give you information about the incoming request, which handler is processing it, the response outcome, etc. If you wish to log all request details (including potentially sensitive information), you can turn on the spring.mvc.log-request-details or spring.codec.log-request-details configuration properties.

Spring MVC 및 Spring WebFlux 응용 프로그램을 개발하는 동안 웹 요청에 대한 자세한 정보가 필요하므로 개발자 도구는 웹 로깅 그룹에 대한 DEBUG 로깅을 활성화합니다. 이렇게 하면 수신 요청, 처리 중인 처리기, 응답 결과 등에 대한 정보가 제공됩니다. 잠재적으로 중요한 정보를 포함하여 모든 요청 세부 정보를 기록하려는 경우 spring.mvc.log-request-details 또는 spring.codec.log-request-details 구성 속성을 설정할 수 있습니다.

### 8.2. Automatic Restart

Applications that use spring-boot-devtools automatically restart whenever files on the classpath change. This can be a useful feature when working in an IDE, as it gives a very fast feedback loop for code changes. By default, any entry on the classpath that points to a directory is monitored for changes. Note that certain resources, such as static assets and view templates, do not need to restart the application.

spring-boot-devtools를 사용하는 응용 프로그램은 클래스 경로의 파일이 변경될 때마다 자동으로 다시 시작됩니다. 이것은 코드 변경을 위한 매우 빠른 피드백 루프를 제공하기 때문에 IDE에서 작업할 때 유용한 기능이 될 수 있다. 기본적으로, 디렉토리를 가리키는 클래스 경로의 모든 항목은 변경사항이 모니터링됩니다. 정적 자산 및 뷰 템플릿과 같은 특정 리소스는 애플리케이션을 재시작할 필요가 없습니다.

Triggering a restart

As DevTools monitors classpath resources, the only way to trigger a restart is to update the classpath. The way in which you cause the classpath to be updated depends on the IDE that you are using:

DevTools가 클래스 경로 리소스를 모니터링하므로 재시작을 트리거하는 유일한 방법은 클래스 경로를 업데이트하는 것입니다. 클래스 경로를 업데이트하는 방법은 사용 중인 IDE에 따라 달라집니다.

- In Eclipse, saving a modified file causes the classpath to be updated and triggers a restart.
- In IntelliJ IDEA, building the project (`Build +→+ Build Project`) has the same effect.
- If using a build plugin, running `mvn compile` for Maven or `gradle build` for Gradle will trigger a restart.

- Eclipse에서 수정된 파일을 저장하면 클래스 경로가 업데이트되고 재시작이 트리거됩니다.
- IntelliJ IDEA에서는 프로젝트를 빌드(빌드 +→+빌드 프로젝트)하는 것과 동일한 효과가 있습니다.
- 빌드 플러그인을 사용하는 경우 Maven에 대해 mvn 컴파일을 실행하거나 Gradle에 대해 gradle 빌드를 실행하면 재시작이 트리거됩니다.

### *Restart vs Reload*

The restart technology provided by Spring Boot works by using two classloaders. Classes that do not change (for example, those from third-party jars) are loaded into a *base* classloader. Classes that you are actively developing are loaded into a *restart* classloader. When the application is restarted, the *restart* classloader is thrown away and a new one is created. This approach means that application restarts are typically much faster than “cold starts”, since the *base* classloader is already available and populated.

Spring Boot에서 제공하는 재시작 기술은 두 개의 클래스 로더를 사용하여 작동합니다. 변경되지 않는 클래스(예: 타사 병에서 가져온 클래스)는 기본 클래스 로더에 로드됩니다. 현재 개발 중인 클래스는 다시 시작 클래스 로더에 로드됩니다. 응용 프로그램이 재시작되면 재시작 클래스 로더가 폐기되고 새 로더가 생성됩니다. 이 접근 방식은 기본 클래스 로더가 이미 사용 가능하고 채워져 있기 때문에 일반적으로 애플리케이션 재시작이 "콜드 스타트"보다 훨씬 빠르다는 것을 의미합니다.

If you find that restarts are not quick enough for your applications or you encounter classloading issues, you could consider reloading technologies such as [JRebel](https://jrebel.com/software/jrebel/) from ZeroTurnaround. These work by rewriting classes as they are loaded to make them more amenable to reloading.

재시작이 응용 프로그램에 충분히 빠르지 않거나 클래스 로딩 문제가 발생하는 경우 제로 턴어라운드의 JRebel과 같은 기술을 다시 로드하는 것을 고려해 볼 수 있습니다. 이러한 작업은 다시 로드하기 쉽게 만들기 위해 클래스가 로드될 때 다시 쓰는 방식으로 작동합니다.

### 8.3. LiveReload

The `spring-boot-devtools` module includes an embedded LiveReload server that can be used to trigger a browser refresh when a resource is changed. LiveReload browser extensions are freely available for Chrome, Firefox and Safari from [livereload.com](http://livereload.com/extensions/).

스프링 부트-개발 도구 모듈에는 리소스가 변경될 때 브라우저 새로 고침을 트리거하는 데 사용할 수 있는 내장형 LiveReload 서버가 포함되어 있습니다. 라이브리로드 브라우저 확장 기능은 livereload.com에서 Chrome, Firefox 및 Safari에 무료로 사용할 수 있습니다.

If you do not want to start the LiveReload server when your application runs, you can set the `spring.devtools.livereload.enabled` property to `false`.

응용 프로그램이 실행될 때 LiveReload 서버를 시작하지 않으려면 spring.devtools.livereload.enabled 속성을 false로 설정할 수 있습니다.

### 8.4. Global Settings

You can configure global devtools settings by adding any of the following files to the `$HOME/.config/spring-boot` directory:

1. `spring-boot-devtools.properties`
2. `spring-boot-devtools.yaml`
3. `spring-boot-devtools.yml`

### 8.5. Remote Applications

The Spring Boot developer tools are not limited to local development. You can also use several features when running applications remotely. Remote support is opt-in as enabling it can be a security risk. It should only be enabled when running on a trusted network or when secured with SSL. If neither of these options is available to you, you should not use DevTools' remote support. You should never enable support on a production deployment.

스프링 부트 개발자 도구는 로컬 개발에만 국한되지 않습니다. 또한 응용프로그램을 원격으로 실행할 때 여러 기능을 사용할 수 있습니다. 원격 지원은 보안 위험이 있으므로 선택할 수 있습니다. 이 옵션은 신뢰할 수 있는 네트워크에서 실행되거나 SSL로 보안된 경우에만 사용하도록 설정해야 합니다. 두 옵션을 모두 사용할 수 없는 경우에는 DevTools의 원격 지원을 사용하지 마십시오. 프로덕션 배포에 대한 지원을 활성화하면 안 됩니다.

## 9. 어플리케이션 패키징

Executable jars can be used for production deployment. As they are self-contained, they are also ideally suited for cloud-based deployment.

For additional “production ready” features, such as health, auditing, and metric REST or JMX end-points, consider adding `spring-boot-actuator`. See *[production-ready-features.html](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/production-ready-features.html#production-ready)* for details.

# 스프링부트 CLI 사용해보기

스프링 부트 CLI(Command Line Interface)는 커맨드라인에서 사용하는 스프링 부트 툴입니다.

스프링 부트 CLI를 이용하면 스프링 프로토타입을 빠르게 만들어 볼 수 있습니다. 

예제를 통해 Groovy 파일 하나로 스프링 부트 앱을 실행해보겠습니다.

[Groovy](https://groovy-lang.org/) 스크립트를 사용하여 실행을 합니다. Groovy는 자바와 비슷한 문법을 가진 *동적 언어* 입니다.

스프링부트 CLI를 설치하는 방법은 여러가지가 있습니다. 저는 macOS 유저에게 친숙한 Homebrew를 이용해 보았습니다.

터미널을 열어서 다음 명령어를 입력합니다.

```bash
$ brew tap spring-io/tap
```

다음과 같이 결과가 출력됩니다.

```bash
Updating Homebrew...
==> Tapping spring-io/tap
Cloning into '/usr/local/Homebrew/Library/Taps/spring-io/homebrew-tap'...
remote: Enumerating objects: 282, done.
remote: Counting objects: 100% (282/282), done.
remote: Compressing objects: 100% (108/108), done.
remote: Total 282 (delta 87), reused 281 (delta 86), pack-reused 0
Receiving objects: 100% (282/282), 41.83 KiB | 247.00 KiB/s, done.
Resolving deltas: 100% (87/87), done.
Tapped 1 formula (27 files, 76KB).
➜  springboot-reference-documentation-study git:(main) ✗ brew install spring-boot
==> Installing spring-boot from spring-io/tap
==> Downloading https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.4.4/spring-boot-cli-2.4.4-bin.tar.gz
######################################################################## 100.0%
...
Possible conflicting files are:
/usr/local/etc/bash_completion.d/spring -> /usr/local/Cellar/springboot/2.3.4.RELEASE/etc/bash_completion.d/spring
/usr/local/bin/spring -> /usr/local/Cellar/springboot/2.3.4.RELEASE/bin/spring
/usr/local/share/zsh/site-functions/_spring -> /usr/local/Cellar/springboot/2.3.4.RELEASE/share/zsh/site-functions/_spring
==> Caveats
zsh completions have been installed to:
  /usr/local/share/zsh/site-functions
==> Summary
🍺  /usr/local/Cellar/spring-boot/2.4.4: 7 files, 13.2MB, built in 3 seconds
```



app.groovy 라는 파일을 생성하여 다음과 같이 입력합니다.

```groovy
@RestController
class ThisWillActuallyRun {
    @RequestMapping("/")
    String home() {
        "Hello Meet Coder~!"
    }
}
```

파일을 다음 명령어를 통해 실행합니다.

```bash
$ spring run app.groovy
```

앱이 구동되는데 필요한 의존성을 설치하는 메시지가 먼저 뜨고 애플리케이션이 정상적으로 구동되는 모습을 볼 수 있습니다!

```
Resolving dependencies....................

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.4.RELEASE)

2021-04-11 17:58:10.711  INFO 55789 --- [       runner-0] o.s.boot.SpringApplication               : Starting application on Chang-Wonui-MacBookPro.local with PID 55789 (started by john in /Users/john/Dev/springboot-sandbox)
2021-04-11 17:58:10.720  INFO 55789 --- [       runner-0] o.s.boot.SpringApplication               : No active profile set, falling back to default profiles: default
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.codehaus.groovy.reflection.CachedClass (jar:file:/usr/local/Cellar/springboot/2.3.4.RELEASE/lib/spring-boot-cli-2.3.4.RELEASE.jar!/BOOT-INF/lib/groovy-2.5.13.jar!/) to method java.lang.Object.finalize()
WARNING: Please consider reporting this to the maintainers of org.codehaus.groovy.reflection.CachedClass
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
2021-04-11 17:58:11.601  INFO 55789 --- [       runner-0] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-04-11 17:58:11.612  INFO 55789 --- [       runner-0] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-04-11 17:58:11.612  INFO 55789 --- [       runner-0] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.38]
2021-04-11 17:58:11.637  INFO 55789 --- [       runner-0] org.apache.catalina.loader.WebappLoader  : Unknown class loader [org.springframework.boot.cli.compiler.ExtendedGroovyClassLoader$DefaultScopeParentClassLoader@7a3d45bd] of class [class org.springframework.boot.cli.compiler.ExtendedGroovyClassLoader$DefaultScopeParentClassLoader]
2021-04-11 17:58:11.682  INFO 55789 --- [       runner-0] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-04-11 17:58:11.683  INFO 55789 --- [       runner-0] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 790 ms
2021-04-11 17:58:11.851  INFO 55789 --- [       runner-0] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-04-11 17:58:12.175  INFO 55789 --- [       runner-0] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-04-11 17:58:12.184  INFO 55789 --- [       runner-0] o.s.boot.SpringApplication               : Started application in 1.934 seconds (JVM running for 29.82)
```

우리가 작성한 API가 작동되는지 확인을 해보겠습니다.

```bash
$ curl -X GET localhost:8080
Hello, Meet Coder~!
```



## 의존성을 추가한 부트 애플리케이션 생성하기

스프링 부트에서 많이 사용하는 web과 data-jpa 의존성을 스프링부트 CLI로 생성해보겠습니다.

```bash
$ spring init --dependencies=web,data-jpa my-project-test
Using service at https://start.spring.io
Project extracted to '/Users/john/Dev/springboot-sandbox/my-project-test'
```

프로젝트가 정상적으로 생성되는 것을 확인할 수 있습니다.



어떤 의존성이 있는지 확인하고 생성하려면 어떻게 할까요?

```bash
$ spring init --list
```



스프링 부트에서 추가가능한 모든 의존성이 출력됩니다.

```bash
 .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
:: Service capabilities ::  https://start.spring.io
=======================================
Capabilities of https://start.spring.io
=======================================

Available dependencies:
-----------------------
actuator - Actuator: Production ready features to help you monitor and manage your application
...
web - Web: Support for full-stack web development, including Tomcat and spring-webmvc
websocket - Websocket: Support for WebSocket development
ws - WS: Support for Spring Web Services

Available project types:
------------------------
gradle-build -  Gradle Config [format:build, build:gradle]
gradle-project -  Gradle Project [format:project, build:gradle]
maven-build -  Maven POM [format:build, build:maven]
maven-project -  Maven Project [format:project, build:maven] (default)
```



이 중에서 spring native를 추가하고 Gradle을 빌드 시스템으로 설정하여 생성해보겠습니다.

```bash
$ spring init --build=gradle --dependencies=native myproject-native
```

```bash
Task :bootRun
2021-04-11 18:11:23.530  INFO 64104 --- [           main] o.s.nativex.NativeListener               : This application is bootstrapped with code generated with Spring AOT

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.4.4)

2021-04-11 18:11:23.568  INFO 64104 --- [           main] c.e.myprojectnative.DemoApplication      : Starting DemoApplication using Java 11.0.8 on Chang-Wonui-MacBookPro.local with PID 64104 (/Users/john/Dev/springboot-sandbox/myproject-native/build/classes/java/main started by john in /Users/john/Dev/springboot-sandbox/myproject-native)
2021-04-11 18:11:23.569  INFO 64104 --- [           main] c.e.myprojectnative.DemoApplication      : No active profile set, falling back to default profiles: default
2021-04-11 18:11:23.997  INFO 64104 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-04-11 18:11:24.004  INFO 64104 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-04-11 18:11:24.004  INFO 64104 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.44]
2021-04-11 18:11:24.048  INFO 64104 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-04-11 18:11:24.048  INFO 64104 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 455 ms
2021-04-11 18:11:24.134  INFO 64104 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-04-11 18:11:24.207  INFO 64104 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-04-11 18:11:24.213  INFO 64104 --- [           main] c.e.myprojectnative.DemoApplication      : Started DemoApplication in 0.885 seconds (JVM running for 1.114)
```





---

https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/getting-started.html#getting-started-installing-the-cli

https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-cli.html




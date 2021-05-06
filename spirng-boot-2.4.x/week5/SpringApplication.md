## [5주차] SpringApplication & Externalized Configuration

## SpringApplication

- SpringApplication 클래스는 main() 메서드에서 시작되는 Spring Application을 실행할 수 있도록 편리한 방법을 제공합니다.
- 기본적으로 정적 메서드로 위임해서 사용합니다.

```jsx
public static void main(String[] args) {
    SpringApplication.run(MySpringConfiguration.class, args);
}
```

- 그러면 아래와 같이 출력이 됩니다.

```jsx
.   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::   v2.4.3

2019-04-31 13:09:54.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
2019-04-31 13:09:54.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
2019-04-01 13:09:56.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
2019-04-01 13:09:57.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)
```

- 기본적으로 INFO 로깅이 나오게 되며, `spring.main.log-startup-info` 의 값을 이용해 로깅을 제거할 수 있습니다.
- debug level을 볼 수 있도록 옵션을 제공합니다.

```jsx
$ java -jar myproject-0.0.1-SNAPSHOT.jar -debug
```

### Lazy Initialzation

```jsx
spring.main.lazy-initialization=true
```

- 애플리케이션 시작 중에 Bean을 생성하는 것이 아니라, 필요하기 전까지 Bean이 초기화 되지 않습니다.
- 그러나 단점은 문제 발견을 하기가 힘들수도 있다는 것이다. 즉, Bean이 초기화 될 때 문제가 발생하게 된다면 문제 발생을 늦게 알 수 있다는 점입니다.
- 또한 메모리가 충분한지 확인하기가 어렵습니다. 따라서 지연 초기화를 사용하게 되면 JVM 힙 크기를 여유롭게 잡는것이 좋습니다.
- 또한 특정 Bean에 대해 지연 초기화를 하지 않으려면 `@Lazy(false)` 를 설정하면 됩니다.

### 배너 설정

- `banner.txt` 를 설정하거나 `spring.banner.location` 을 이용해 배너를 변경할 수 있습니다.
- 또한 `spring.banner.charset` 을 통해 인코딩을 설정할 수 있고, `spring.banner.image.location` 을 통해 이미지도 가능합니다.
- 배너를 위한 변수가 있습니다.
- ${application.version} : 애플리케이션 버전
- ${application.formatted-version} : 애플리케이션 버전 포맷
- ${spring-boot.version} : 사용중인 스프링 부트 버전
- ${spring-boot.formatted-version} : 사용중인 스프링 부트 버전 포맷
- ${application.title} : 애플리케이션 제목
- 배너는 SpringBootBanner Class에 싱글 톤 Bean으로 등록됩니다.

### SpringApplication Custom

- 기본적으로 정적 메서드를 이용해 사용하지만, 인스턴스를 사용해 Custom을 할 수 있습니다.
- 여기서 할 수 있는 것들은 모두 `[application.properties](http://application.properties)` 에서 사용 가능합니다.

```jsx
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(MySpringConfiguration.class);
    app.setBannerMode(Banner.Mode.OFF);
    app.run(args);
}
```

- 현재 위의 예제는 Banner를 사용하지 않겠다는 의미입니다.

### Fluent Builder

- 빌더패턴을 이용해 Spring Application을 사용할 수 있습니다.

```jsx
new SpringApplicationBuilder()
        .sources(Parent.class)
        .child(Application.class)
        .bannerMode(Banner.Mode.OFF)
        .run(args);
```

- 여기서 주요한건은 웹 구성 요소는 자식 컨테이너에 포함되어야 하며, Enviroment는 부모와 자식 둘다 적용됩니다.

### ApplicationRunner vs CommandLineRunner

- CommneandLineRunner

```jsx
@FunctionalInterface
public interface CommandLineRunner {

    /**
     * Callback used to run the bean.
     * @param args incoming main method arguments
     * @throws Exception on error
     */
    void run(String... args) throws Exception;

}
```

- `@Component` 를 이용해 빈으로 등록하면 스프링 초기화 작업을 마치고 나서 해당 클래스 run 메서드를 실행시켜준다.
- ApplicationRunner

```jsx
@FunctionalInterface
public interface ApplicationRunner {

    /**
     * Callback used to run the bean.
     * @param args incoming application arguments
     * @throws Exception on error
     */
    void run(ApplicationArguments args) throws Exception;

}
```

- CommandLineRunner와 하는 일은 동일하지만 arguments만 다르게 된다.
- CommandLineRunner보다 더 최신에 만들어졌기 때문에 이걸 사용하는 것이 좋다.

## Externalized Configuration

- 아래와 같은 순서로 진행됩니다.

1. Default properties (specified by setting `SpringApplication.setDefaultProperties`).

1. 기본 설정 (SpringApplication.setDefaultProperties)

2. @PropertySource, @cocnfiuration의 설정 (logging, main은 그 전에 읽게 됩니다.)

3. application.properties

4. RandomValuePropertySourcde

5. OS 환경 변수

6. Java 속성

7. JNDI 속성

8. ServletContext

9. ServletConfig

10. SPRING_APPLCATION_JSON

11. Command line arguments.

12. @SpringBootTest

13. @TestPropertySource

### Command line arguments.

- 시작시 `--`  로 시작하는 명령어로 예를 들면 `--server.port=9000` 과 같은 것이다.

```jsx
SpringApplication.setAddCommandLineProperties(false). // 비활성화
```

### External Application Properties

- application.properties, application.yaml은 기본적으로 아래와 같은 순서로 찾게 됩니다.
1. classpath root
2. classpath `/config`
3. current directory
4. `/config` subdirectory
5. `/config` 하위 디렉토리
- `spring.config.location` 속성을 이용해 custom한 속성 디렉토리를 설정할 수 있습니다.
- `[spring.config.name](http://spring.config.name)` 으로 application이 아닌 다른 이름으로 설정할 수 있습니다.

### Profile Specific Files

- `application-{profile}` 이걸 통해 profile에 맞는 환경설정을 할 수 있습니다.
- `[spring.profiles.active](http://spring.profiles.active)` 속성을 통해 default를 설정할 수 있습니다.

### Type-safe Configuration Properties

[@Value & @ConfigurationProperties](https://github.com/ksy90101/TIL/blob/master/spring/value-configuration-properties-yaml-mapping.md)

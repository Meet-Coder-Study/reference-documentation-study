# Using Spring boot

`해당 문서는 Spring boot 공식 문서를 읽고 공부한 내용입니다 원본은 아래의 링크를 클릭 !`  
[Using Spring Boot](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-build-systems)

# Build Systems

 - `의존성을 관리하고, MavenCentral 같은 중앙 저장소에서 이 종속성들을 publish 할 수 있는 빌드도구를 적극적으로 추천한다.`  
 - `Maven 과 Gradle 을 사용하는 것을 추천하며, Ant 를 사용할 수 있긴 하다.`

스프링은 서로 호환성이 좋은 라이브러리의 세트를 제공한다.
또한 이 라이브러리들이 어떤 버전을 사용하면 좋은지 미리 모두 정의해두었다.  

빌드도구들은 이 정보들을 쉽게 가져오게 도와주는데, 어떻게 도와주는지 알아보자.

<br>
<br>

## Maven

[Spring Boot Maven Plugin Documentation](https://docs.spring.io/spring-boot/docs/2.4.3/maven-plugin/reference/htmlsingle/)

 - Spring-boot-starter-parent 를 들어가보면, spring-dependencies 가 있고 그 안에서 현재 스프링부트 버전에 최적화된 버전을 모두 명시해두었다.
 - 빌드 도구는 그 부분에 명시된 버전의 라이브러리를 자동으로 다운로드해준다.
 - 라이브러리가 현재 스프링 버전과 호환되는 버전을 찾는 것 자체가 일이고 노동인데 이를 도와준다. 
 
## Gradle

[Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.4.3/gradle-plugin/reference/htmlsingle/)

 - gradle 에서는 plugin 을 통해 이를 도와준다.
 - `id 'org.springframework.boot' version '2.4.3'` 플러그인은 프로젝트를 본격적으로 설정하는 플러그인은 아니지만, 이후의 플러그인들이 들어오는 것을 감지하는 역할을 한다.
 - 예를 들어 아래의 플러그인들은 자동으로 생성되며, 위의 플러그인이 감지하고 실행한다.
 ```
apply plugin: 'java'
apply plugin: 'io.spring.dependency-management'
```
 - [apply plugin: 'io.spring.dependency-management'](https://github.com/spring-gradle-plugins/dependency-management-plugin) 는 maven 의 starter-parent 와 같은 역할을 한다.
    - 스프링 부트에서 제공하는 [라이브러리 버전 정의](https://docs.spring.io/spring-boot/docs/2.4.3/reference/htmlsingle/#dependency-versions-coordinates) 를 자동으로 한다.
    - 이를 통해 버전을 명시하지 않고 의존성을 관리할 수 있다.

<br>
<br>

## starter
 - starter 는 `의존성 모음` 이다.
 - 예를들어 spring boot 로 web 개발을 진행하고 싶다면, tomcat 부터 시작해서 thymeleaf 등등 여러가지 의존성 라이브러리가 필요하다.
 - 하지만 `'org.springframework.boot:spring-boot-starter-web'` 의존성 관리는 그 안에 필요한 모든 것을 한번에 다운받도록 도와준다.
 - `spring-boot-starter-*` 의 이름 패턴을 가진다. (검색하기 쉽게 이름 패턴을 고정시켰다고 한다.)
 
 [여기](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-starter) 서 현재 버전의 스프링부트가 제공하는 starter 들을 볼 수 있다.

<br>
<br>

#### spring-boot-starter-actuator
 - 에플리케이션을 모니터링하고 관리하는데 필요한 라이브러리셋을 제공한다.
 - 이 정보를 이용하여 UI 적으로 보기 쉽게 만든 오픈소스 프로젝트가 있는데, spring boot admin 이라고 한다.
    - [Spring Boot Admin Github](https://github.com/codecentric/spring-boot-admin)
 ```
<dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-starter-server</artifactId>
        <version>2.1.4</version>
    </dependency>
```

<br>
<br>


## Structuring Your Code

스프링 부트에서는 정해진 코드의 구조는 없다. 하지만 다음의 사항들은 권장되고 있다.
 - default 패키지 사용은 지양한다.
    - default 패키지란 패키지를 지정하지 않는 기본 설정이다.
    - root 디렉토리이다.
    - @ComponentScan, @EntityScan 등 특정 에너테이션에서 문제가 발생할 수 있다.
 - 기본 애플리케이션 클래스 즉, @SpringBootApplication 에너테이션이 붙은 클래스는 Root 패키지에 속하는 것이 좋다.
    - @SpringBootApplication 의 위치는, @ComponentScan, @EntityScan 등 검색이 필요한 에너테이션들의 검색 패키지 위치를 정의한다.
    - 예를 들어, JPA 애플리케이션을 작성하는 경우 @SpringBootApplication 이 달린 클래스 의 패키지가 @Entity 을 검색하는 데 사용된다. 

@SpringbootApplication 에너테이션에 대해서는 이후에 자세히 다룬다.

<br>
<br>

# Configuration Classes

Spring boot 이전에는 빈을 등록하기 위해 xml 을 사용했다. Spring boot 에서도 여전히 XML 을 사용하여 빈을 등록할 수 있지만, @Configuration 어노테이션이 붙은 클래스를 이용하는 것을 추천한다.

<br>

## @Configuration

 - @Configuration 이 붙은 클래스는 내부에 @Bean 에너테이션이 붙은 메서드들을 실행하여 빈을 등록시킨다.

 ```java
@Configuration
public class AppConfig {

    @Bean
    public MyBean getBeanName(){
        return new MyBeanImpl();
    }
}
```

- @Configuration 자신이 붙은 클래스도 빈으로 등록시킨다.
```java
	@Test
	void Configuration_에너테이션은_빈으로_등록되는가() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(SpringbootDocsApplication.class);

		Object bean = ac.getBean(SpringbootDocsApplication.class);

		Assertions.assertThat(bean).isInstanceOf(SpringbootDocsApplication.class);
	}
```
<br>

## @Import

 - `@Import({Config1.class, Config2.class})` 와 같이 다른 클래스를 가져올 수 있다.
 - 가져올 클래스에 @Configuration 이 붙어있지 않더라도, 가져온 클래스에 붙어있으면 적용된다.
    - 상속과 비슷하다.
    - 상속에 비해 위 구문처럼 여러개를 불러올 수 있고, 에너테이션 클래스도 선언가능하는 등 유연성이 더 좋다.
    
 ```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Import({Config1.class, Config2.class})
public @interface EnableConfig {

  boolean show() default true;
}
```
 - 이런 식으로 에너테이션에도 붙일 수 있어서, 기능이 추가된 설정파일을 만들 수 있다.
 
 
 <br>
 <br>
 
 
 
# Auto-configuration

Spring Boot 자동 구성은 build.gradle 또는 pom.xml 에 사용자가 정의한 jar 의존성들을 기반으로 에플리케이션을 자동구성한다.

 - @EnableAutoConfiguration 또는 이 에너테이션이 포함된 @SpringBootApplication 둘 중 하나가 붙은 클래스를 사용해야한다.


<br>


## 자동 구성 라이브러리 알아보기

현재의 자동 구성 라이브러리를 알기 위해서는 `--debug` 옵션을 활성화 시키고 프로그램을 실행시킨다.

- debug 옵션을 활성화 시키는 방
    ```
    // application.properties
    debug=true
  
    // yaml
    debug: true
    ```

- 명령줄에서 디버그 모드로 실행시키기
    ```bash
    java -jar [패키지된 파일이름].jar --debug
    ```

- --debug 모드로 실행시키면 콘솔창의 결과
```
CONDITIONS EVALUATION REPORT
============================


Positive matches:
-----------------

   AopAutoConfiguration matched:
      - @ConditionalOnProperty (spring.aop.auto=true) matched (OnPropertyCondition)

   AopAutoConfiguration.ClassProxyingConfiguration matched:
      - @ConditionalOnMissingClass did not find unwanted class 'org.aspectj.weaver.Advice' (OnClassCondition)
      - @ConditionalOnProperty (spring.aop.proxy-target-class=true) matched (OnPropertyCondition)

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet' (OnClassCondition)
      - found 'session' scope (OnWebApplicationCondition)

...
```
<br>

## 자동 구성한 설정 제외하기

위의 과정을 통해 어떤 라이브러리가 자동구성되는지 알았다. 만약 특정 자동구성을 사용하고 싶지 않으면 아래의 3가지 방법으로 할 수 있다.

 - @SpringBootApplication 의 exclude 옵션으로 제외할 클래스이름 적용
    - `@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})`
 - classpath 에 없다면 excludeName 옵션을 통해 풀네임을 지정할 수 있음
    - `@SpringBootApplication(excludeName = "org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration")`
 - yaml, properties 에서 `spring.autoconfigure.exclude` 옵션을 통해 조절하는 방법.

이 방법들은 @SpringBootApplication 에 지정할 수 있으며, @EnableAutoConfiguration 에도 가능하다.
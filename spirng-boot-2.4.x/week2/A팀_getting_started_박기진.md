# 2. Getting started

- 스프링 공홈에서 스프링을 시작하는 가이드대로 그대로 따라하면 되지만 들어가기 앞서 중요한 개념을 이해하고 가자

## 1. Introducing Spring boot
https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/getting-started.html#getting-started-introducing-spring-boot


## Ioc/DI (제어의 역전/의존성 주입)

- IoC는 스프링만의 고유한 개념이 아니다.
- 객체의 생명주기 관리, 흐름제어 그리고 권한을 빈 팩토리에 위임하는 프로그래밍 모델
- DI는 IoC를 구현하는 방식중에 하나이다
- DI를 통해 모듈간의 결합도가 낮아지도 유연성이 낮아진다.
- 객체의 생명주기를 스프링 프레임워크에 위임함으로써? -> 비지니스 로직에 집중할수 있다.

```
Aggregation : 집한관계
- 부분이 전체의 다른 생명 주기를 가질수 있따.
ex) 컴퓨터와 키보드
컴퓨터와 키보드는 관계를 가지고 있으나 키보느다 없다고 하여 컴퓨터가 안돌아가지는 않는다.


Commposition : 구성관계
- 부분은 전체와 같은 생명 주기를 갖는다.
ex) 자동차와 엔진
자동차와 엔진은 관계를 가지고 있으며 엔진이 없을 경우 자동차는 더이상 움직이지 않는다.
```

#### 1. 생성자를 통한 의존성 주입
Keyboard keyboard = new RealPorceKeyBoard();
Computer computer = new AppleComputer(keyboard);

유저가 키보드를 선택한다.
유저가 컴퓨터를 생산하며 키보드를 설정한다.

#### 2. setter를 통한 의존성 주입
Keyboard keyboard = new RealPorceKeyBoard();
Computer computer = new AppleComputer();
computer.setKeyBoard(keyboard);


## 2. Maven vs Gradle 

what is maven? what is gradle? 
-> 자동 빌드 도구

왜 ant에서 개발자는 maven과 gradle에 열광하는가?
- and 자유도가 너무 높음 -> Configuration over Convention의 필요성
- ant는 형식적인 규칙이 없으며 프로젝트에 종속적
- ant는 스크립트 등등을 직접 짜야함(통일 및 획일화 되지 않음)


## 3. @SpringBootApplication

처음 스프링부트를 가이드를 따라 실행하고 정상적으로 작동했다면 SpringBootApplication어노테이션을 알아보자. 특별한 설정하나도 없이 프로젝트 최상단의 @SpringBootApplication을 통해 모든 것이 실행된다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```

@EnableAutoConfiguration 
- Auto-Configuration 메커니즘

@ComponentScan
- 패키지상의 모든 @Component를 스캔한다


번외로 @Configuration을 추후 더 집중적으로 봐야한다.
- @Configuration은 그대로 빈으로 등록하지 않고 바이트 조작이 이루어진다.
- 스프링이 CGLIB을 사용하여 바이트코드를 조작한다. 

## 4. Application Context? BeanFactory?

ApplicationContext

- 빈팩토리를 상속하여 제공
- 빈팩토리의 기능말고도 다양한 기능들을 제공한다.

```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver{}

```

BeanFactory
- 스프링 컨테이너
- 빈을 등록, 생성, 조회, 반환을 관리함



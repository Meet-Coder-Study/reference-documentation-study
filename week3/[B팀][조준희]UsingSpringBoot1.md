# 2. Using Spring Boot (1 / 2)

### **2.1. Using the “default” Package**

# 1. 빌드 시스템

It is strongly recommended that you choose a build system that supports dependency management and that can consume artifacts published to the “Maven Central” repository. We would recommend that you choose Maven or Gradle. It is possible to get Spring Boot to work with other build systems (Ant, for example), but they are not particularly well supported.

종속성 관리를 지원하고 "Maven Central"저장소에 게시 된 아티팩트를 사용할 수있는 빌드 시스템을 선택하는 것이 좋습니다. Maven 또는 Gradle을 선택하는 것이 좋습니다. Spring Boot가 다른 빌드 시스템 (예 : Ant)과 함께 작동하도록 할 수 있지만 특별히 잘 지원되지는 않습니다.

### 1.1. Dependency Management

Each release of Spring Boot provides a curated list of dependencies that it supports. In practice, you do not need to provide a version for any of these dependencies in your build configuration, as Spring Boot manages that for you. When you upgrade Spring Boot itself, these dependencies are upgraded as well in a consistent way.

Spring Boot의 각 릴리스는 지원하는 선별 된 종속성 목록을 제공합니다. 실제로 Spring Boot에서 관리하므로 빌드 구성에서 이러한 종속성에 대한 버전을 제공 할 필요가 없습니다. Spring Boot 자체를 업그레이드하면 이러한 종속성도 일관된 방식으로 업그레이드됩니다.

(물론 필요하다면 버전을 직접 지정할 수도 있습니다. - 버전 오버라이딩)

The curated list contains all the Spring modules that you can use with Spring Boot as well as a refined list of third party libraries. The list is available as a standard Bills of Materials (spring-boot-dependencies) that can be used with both Maven and Gradle.

큐레이팅 된 목록에는 Spring Boot와 함께 사용할 수있는 모든 Spring 모듈과 정제 된 타사 라이브러리 목록이 포함되어 있습니다. 이 목록은 Maven 및 Gradle 모두에서 사용할 수있는 표준 Bills of Materials(BOM - 상세내역서) (spring-boot-dependencies)으로 제공됩니다.

### 1.2. Maven

### 1.3. Gradle

[Maven vs Gradle](https://bkim.tistory.com/13)

[Gradle 설정 파일 및 기본 개념 - SLiPP](https://www.slipp.net/wiki/pages/viewpage.action?pageId=11632748)

[https://docs.gradle.org/current/userguide/tutorial_using_tasks.html](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)

### 1.4. Ant

### 1.5. Starters

Starters are a set of convenient dependency descriptors that you can include in your application. You get a one-stop shop for all the Spring and related technologies that you need without having to hunt through sample code and copy-paste loads of dependency descriptors. For example, if you want to get started using Spring and JPA for database access, include the `spring-boot-starter-data-jpa` dependency in your project.

The starters contain a lot of the dependencies that you need to get a project up and running quickly and with a consistent, supported set of managed transitive dependencies.

스타터는 애플리케이션에 포함 할 수있는 편리한 종속성 설명자 세트입니다. 샘플 코드를 검색하고 많은 종속성 설명자를 복사하여 붙여 넣을 필요없이 필요한 모든 Spring 및 관련 기술을 원 스톱으로 구매할 수 있습니다. 예를 들어 데이터베이스 액세스를 위해 Spring 및 JPA를 사용하기 시작하려면 프로젝트에 spring-boot-starter-data-jpa 종속성을 포함하십시오.

스타터에는 프로젝트를 빠르게 시작하고 실행하는 데 필요한 많은 종속성과 일관되고 지원되는 관리되는 전이 종속성 집합이 포함되어 있습니다.

What’s in a name

All **official** starters follow a similar naming pattern; `spring-boot-starter-*`, where `*` is a particular type of application. This naming structure is intended to help when you need to find a starter. The Maven integration in many IDEs lets you search dependencies by name. For example, with the appropriate Eclipse or STS plugin installed, you can press `ctrl-space` in the POM editor and type “spring-boot-starter” for a complete list.

As explained in the “[Creating Your Own Starter](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-custom-starter)” section, third party starters should not start with `spring-boot`, as it is reserved for official Spring Boot artifacts. Rather, a third-party starter typically starts with the name of the project. For example, a third-party starter project called `thirdpartyproject` would typically be named `thirdpartyproject-spring-boot-starter`.

모든 공식 스타터는 비슷한 이름 지정 패턴을 따릅니다. spring-boot-starter- *, 여기서 *는 특정 유형의 애플리케이션입니다. 이 이름 지정 구조는 스타터를 찾아야 할 때 도움을주기위한 것입니다. 많은 IDE의 Maven 통합을 통해 이름으로 종속성을 검색 할 수 있습니다. 예를 들어 적절한 Eclipse 또는 STS 플러그인이 설치된 상태에서 POM 편집기에서 Ctrl-space를 누르고 전체 목록을 보려면 "spring-boot-starter"를 입력 할 수 있습니다.

“Creating Your Own Starter”섹션에서 설명했듯이 써드 파티 스타터는 공식 Spring Boot 아티팩트 용으로 예약되어 있으므로 spring-boot로 시작해서는 안됩니다. 오히려 타사 스타터는 일반적으로 프로젝트 이름으로 시작합니다. 예를 들어 thirdpartyproject라는 타사 시작 프로젝트의 이름은 일반적으로 thirdpartyproject-spring-boot-starter입니다.

[Spring Boot application starters](https://www.notion.so/2565a5a5b0384a2f8e5f1e3b48f227e7)

## 2. 코드 구성하기

Spring Boot는 작동하는 데 특정 코드 레이아웃이 필요하지 않습니다. 그러나 도움이되는 몇 가지 모범 사례가 있습니다.

### 2.1. Using the “default” Package

클래스에 패키지 선언이 포함되지 않은 경우 "기본 패키지"에있는 것으로 간주됩니다. "기본 패키지"의 사용은 일반적으로 권장되지 않으며 피해야합니다. 모든 jar의 모든 클래스를 읽으므로 @ComponentScan, @ConfigurationPropertiesScan, @EntityScan 또는 @SpringBootApplication 주석을 사용하는 Spring Boot 애플리케이션에 특정 문제가 발생할 수 있습니다.

> 자바의 권장 패키지 이름 지정 규칙을 따르고 reversed 도메인 이름 (예 : com.example.project)을 사용하는 것이 좋습니다.

### 2.2. Locating the Main Application Class

일반적으로 다른 클래스 위에있는 루트 패키지에서 기본 애플리케이션 클래스를 찾는 것이 좋습니다. @SpringBootApplication 주석은 종종 기본 클래스에 배치되며 특정 항목에 대한 기본 "검색 패키지"를 암시 적으로 정의합니다. 예를 들어 JPA 애플리케이션을 작성하는 경우 @SpringBootApplication 주석이 달린 클래스의 패키지가 @Entity 항목을 검색하는 데 사용됩니다. 루트 패키지를 사용하면 component scan을 프로젝트에만 적용 할 수도 있습니다.

```
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

## 3. 설정 클래스

Spring Boot는 Java 기반 구성을 선호합니다. XML 소스와 함께 SpringApplication을 사용할 수 있지만 일반적으로 기본 소스가 단일 @Configuration 클래스 인 것이 좋습니다. 일반적으로 기본 메서드를 정의하는 클래스는 기본 @Configuration으로 좋은 후보입니다.

### 3.1. Importing Additional Configuration Classes

모든 @Configuration을 단일 클래스에 넣을 필요는 없습니다. @Import 주석을 사용하여 추가 구성 클래스를 가져올 수 있습니다. 또는 @ComponentScan을 사용하여 @Configuration 클래스를 포함한 모든 Spring 구성 요소를 자동으로 선택할 수 있습니다.

### 3.2. Importing XML Configuration

XML 기반 구성을 반드시 사용해야하는 경우 @Configuration 클래스로 시작하는 것이 좋습니다. 그런 다음 @ImportResource 주석을 사용하여 XML 구성 파일을로드 할 수 있습니다.

## 4. 자동 설정 (Auto-configuration)

Spring Boot 자동 구성은 추가 한 jar 종속성을 기반으로 Spring 애플리케이션을 자동으로 구성하려고합니다. 예를 들어 HSQLDB가 클래스 경로에 있고 데이터베이스 연결 빈을 수동으로 구성하지 않은 경우 Spring Boot는 메모리 내 데이터베이스를 자동 구성합니다.

@Configuration 클래스 중 하나에 @EnableAutoConfiguration 또는 @SpringBootApplication 주석을 추가하여 자동 구성에 옵트 인해야합니다.

@SpringBootApplication 또는 @EnableAutoConfiguration 주석을 하나만 추가해야합니다. 일반적으로 기본 @Configuration 클래스에만 둘 중 하나를 추가하는 것이 좋습니다.

### 4.1. Gradually Replacing Auto-configuration

자동 구성은 비 침습적(non-invasive)입니다. 언제든지 자동 구성의 특정 부분을 대체하기 위해 고유 한 구성을 정의 할 수 있습니다. 예를 들어, 고유 한 DataSource bean을 추가하면 기본 임베디드 데이터베이스 지원이 취소됩니다.

현재 적용되는 자동 구성과 그 이유를 확인해야하는 경우 --debug 스위치를 사용하여 애플리케이션을 시작합니다. 이렇게하면 선택한 코어 로거에 대한 디버그 로그가 활성화되고 콘솔에 조건 보고서가 기록됩니다.

### 4.2. Disabling Specific Auto-configuration Classes

원하지 않는 특정 자동 구성 클래스가 적용되고있는 경우 @SpringBootApplication의 exclude 속성을 사용하여 다음 예와 같이 비활성화 할 수 있습니다.

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;

@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
public class MyApplication {
}
```

클래스가 클래스 경로에없는 경우 주석의 excludeName 속성을 사용하고 대신 완전한 이름을 지정할 수 있습니다. @SpringBootApplication 대신 @EnableAutoConfiguration을 사용하려는 경우 exclude 및 excludeName도 사용할 수 있습니다. 마지막으로 spring.autoconfigure.exclude 속성을 사용하여 제외 할 자동 구성 클래스 목록을 제어 할 수도 있습니다.

You can define exclusions both at the annotation level and by using the property.

annotation level과 property를 사용하여 제외를 정의 할 수 있습니다.

> 자동 구성 클래스는 public이지만 공용 API로 간주되는 클래스의 유일한 aspect는 자동 구성을 비활성화하는 데 사용할 수있는 클래스의 이름입니다. 중첩 된 구성 클래스 또는 빈(bean) 메서드와 같은 클래스의 실제 내용은 내부 전용이므로 직접 사용하지 않는 것이 좋습니다.

# 2. Getting started

## Spring boot
특징
```
- 내장 톰켓, 제티, 언더 토우 같은 WAS를 사용하여 독립 실행이 가능한 스프링 애플리케이션 개발
- 통합 스타터를 제공하여 메이블 / 그레이들 구성 간소화 및 자동화된 의존성 제공
- XML 설정 요구 x
- JAR 파일로 배포 가능
- 액츄에이터 제공
```

<br>

## Gradle
(1) 장점
```
- Maven의 상속 구조와는 다르게 설정 주입 방식을 사용하는 Gradle은 훨씬 더 유연하게 프로젝트 설정 
- 동적인 행위에 제약이 없다.
- Gradle Wrapper를 사용하면 Gradle 설치 없이 어느 환경에서든 프로젝트 빌드 가능 
```

(2) dir 구조

  <img width="300" src="https://user-images.githubusercontent.com/60383031/114418838-db13e280-9bed-11eb-8977-c9be49055942.png">


(2) 설정 파일
```
- gradlew: 리눅스, 맥 OS용 셀 스크립트
- gradlew.bat: 윈도우용 배치 스크립트
- gradle/wrapper/gradle.jar: Gradle 를 다운로드 하기 위한 코드가 들어 있는 Wrapper JAR 파일, 파일 무결성을 위해 git에 저장된 gradle과 checkSum 비교를 통해 무결성 검증 
- gradle/wrapper/gradle-wrapper.properties: 그레이들 설정 정보 프로퍼티 파일
```

<br>

## Annotation
(1) @RestController
```
- Stereotype anntotation으로 불림
- 해당 애노테이션을 보면 어떤 역할을 하는지 알 수 있음
- @Controller에 @ResponseBody 가 추가된 어노테이션
```
(2) @RequestMapping
````
- 라우팅 역할을 담당함
````
(3) EnableAutoConfiguration
```
- @SpringBootConfiguration 에서 사용하는 어노테이션
- 조건에 따라 Bean을 등록
- 사용자 정의 Bean이 등록된 이후에 실행된다.
```

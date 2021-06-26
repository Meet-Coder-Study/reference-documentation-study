# **Container Images**

<br>
<br>

## ✔️ Spring Boot Application을 <code>**Docker Image**</code>로 만들기

<br>
<br>

### **1. Docker Images Layer**
- 계층구조이며, 이를 이용해 같은 Layer 계층은 재활용할 수 있다.
- Spring Boot에서 Jar 파일을 만들 때 Application을 4개의 계층으로 분리해서 만드는 방법을 제공한다.

<br>

### **2. Spring Boot Application 4계층**
- 기준: 변경 빈도
- 정렬: Docker / OCI 이미지에 추가되어야 하는 순서

<br>[layers.idx]
```java
- "dependencies":
  - BOOT-INF/lib/library1.jar
  - BOOT-INF/lib/library2.jar
- "spring-boot-loader":
  - org/springframework/boot/loader/JarLauncher.class
  - org/springframework/boot/loader/jar/JarEntry.class
- "snapshot-dependencies":
  - BOOT-INF/lib/library3-SNAPSHOT.jar
- "application":
  - META-INF/MANIFEST.MF
  - BOOT-INF/classes/a/b/C.class
```

<br>

### **dependencies 계층**
- Spring Boot Application에서 사용되는 라이브러리 중 Release 라이브러리들이 있는 계층
- 변경 빈도: ⭐️⭐️
- 동일한 버전에서 변경이 없기 때문에 거의 변경이 없다.
- ex) 버전을 올려 새 버전으로 정식 출시하는 상황

<br>

### **spring-boot-loader 계층**
- org.springframework.boot.loader 패키지 아래의 모든 패키지와 클래스들이 있는 계층
- 변경 빈도: ⭐️⭐️⭐️
- spring boot에서 제공하는 클래스 중 Loader 패키지에 속한 클래스들의 변경이 있다.

<br>

### **snapshot-dependencies 계층**
- Spring Boot Application에서 사용되는 라이브러리 중 snapshot 라이브러리들이 있는 계층
- 변경 빈도: ⭐️⭐️⭐️⭐️
- snapshot 라이브러리 특성상 계속 개선이 되다 보니, 여러 라이브러리들 중 변경이 잦은 계층이다.
- ex) 라이브러리에서 원하는 기능이 정식 버전에서 지원되지 않아 해당 라이브러리를 개발하는 개발 주체가 다음 버전에서 이 기능을 반영하기 위해 먼저 snapshot을 만든 상황

<br>

### **application 계층**
- Spring Boot Application을 구성하는 class 및 resource들이 있는 계층
- 변경 빈도: ⭐️⭐️⭐️⭐️⭐️
- 직접 만드는 java class와 resource로 구성되어 있기 때문에 수정, 삭제 등 가장 변경이 심한 계층이다.

<br>
<br>

## **3. Docker Images**
- 변경된 내용이 없으면 해당 Layer는 cache되어 재사용이 가능해진다.
- 변경사항이 발생하면 새로 Layer를 만들어서 이미지를 구성한다.
- 본 특성 때문에 Docker 이미지 구성의 효율을 높이기 위해서 변경 빈도에 따라 계층을 나누고, 이를 이용해 Docker 이미지를 만든다.

<br>

### **Spring Boot Application Build**
- Build할 때 계층구조 형태를 가지도록 설정해야 한다.
<br>[gradle.build 설정]

```java
bootJar {
	layered() {
        // includeLayerTools = false : 종속성 제외
    }
}
```
+) Layer 구조의 jar 파일 생성 시, <code>spring-boot-jarmode-layertools</code> jar가 jar 파일 안에 포함된다. 특수모드(계층 추출)에서 Application을 실행할 수 있다. 본 종속성을 제외시키려면 <code>includeLayerTools</code>를 false로 설정한다.

<br>

- Application에 따라 Layer 생성 방법을 조정하거나, 새 Layer를 추가할 수 있다.
- jar의 Layer 분리 방법과 Layer의 순서를 설정한다.
<br> [gradle.build]
```java
bootJar {
	layered {
		application {
			intoLayer("spring-boot-loader") {
				include "org/springframework/boot/loader/**"
			}
			intoLayer("application")
		}
		dependencies {
			intoLayer("snapshot-dependencies") {
				include "*:*:*SNAPSHOT"
			}
			intoLayer("dependencies")
		}
		layerOrder = ["dependencies", "spring-boot-loader", "snapshot-dependencies", "application"]
	}
}
```

<br>
<br>


## **4. Dockerfile**
- 계층화를 통해 최적화된 Docker Image를 만든다.
- default로 <code>spring-boot-jarmode-layertools</code> jar가 jar에 종속성으로 추가된다.
- 특수모드(계층 추출)에서 Application을 실행할 수 있다.
- 본 종속성을 제외시키려면 <code>includeLayerTools</code>를 false로 설정한다.

<br>

### **layertools**
- <code>layertools</code> 모드는 실행 스크립트를 포함하는 <code>완전히 실행 가능한 Spring Boot 아카이브</code>와 함께 사용할 수 없다.
- build 시, <code>layertools</code>와 함께 사용할 jar 파일 시작 스크립트 구성을 비활성화한다.

<br>[layertools 모드로 jar 실행]
```java
$ java -Djarmode=layertools -jar my-app.jar
```
<br>[결과]
```java
Usage:
  java -Djarmode=layertools -jar my-app.jar

Available commands:
  list     List layers from the jar that can be extracted
  extract  Extracts layers from the jar for image creation
  help     Help about any command
```

<br>

- build한 jar 파일 압축을 풀어 Docker 이미지에 올려 사용한다.
- Spring Boot Application을 Dockerfile에 추가할 Layer로 분할한다.
<br>[extract 옵션]
```java
$ java -Djarmode=layertools -jar my-app.jar extract
```

<br>

[jarmode를 사용하는 Dockerfile]
```java
FROM adoptopenjdk:11-jre-hotspot as builder
WORKDIR application
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} application.jar
RUN java -Djarmode=layertools -jar application.jar extract

FROM adoptopenjdk:11-jre-hotspot
WORKDIR application
COPY --from=builder application/dependencies/ ./
COPY --from=builder application/spring-boot-loader/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/application/ ./
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

<br>

- 위 Dockerfile이 현재 디렉토리에 있을 경우, 다음 예시와 같이 Docker 이미지는 <code>docker build .</code>로 빌드하거나 선택적으로 애플리케이션 jar의 경로를 지정할 수 있다.
```java
docker build --build-arg JAR_FILE = path / to / myapp.jar.
```

<br>

- multi-stage Dockerfile로, builder stage는 나중에 필요한 디렉토리를 추출한다.
- 각 <code>COPY</code>명령은 <code>jarmode</code>에 의해 추출된 레이어와 관련된다.
- <code>unzip</code>과 <code>mv</code>의 조합을 사용해 적절한 Layer로 이동할 수 있으나, <code>jamode</code>가 이를 단순화하여 제공한다.

<br>
<br>
<br>

출처
- https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-container-images
- https://zgundam.tistory.com/180
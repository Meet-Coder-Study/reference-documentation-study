# Deploying Spring Boot Applications
## 1.  Deploying to Containers
- 일반적인 배포
    ```java
    $ java -jar myapp.jar
    ```

- Cloud Foundry 방식
    ```java
    $ jar -xf myapp.jar
    $ java org.springframework.boot.loader.JarLauncher
    ```
    - 일반적인 jar 실행보다 약간 더 빠르게 시작된다.    
    - 배포하기 전에 아카이브를 unpack 함

    
## 2.  Deploying to the Cloud
#### (1) Kubernetes
- 쿠버네티스에서 생명 주기 관리 예시
    ```
    spec:
      containers:
      - name: example-container
        image: example-image
        lifecycle:
          preStop:
            exec:
              command: ["sh", "-c", "sleep 10"]
    ```
- graceful shutdown 역할을 한다.
- preStop이 완료되면 SIGTERM이 컨테이너로 전송되고 종료가 시작되기 때문에 종료 직전에 요청받은 작업들을 끝마칠 수 있다.
- 스프링 부트(2.3 이상)에서도 자체적으로 graceful shutdown 기능을 지원하기도 한다.
    ```java
    server.shutdown=graceful
    ```

#### (2) Amazon Web Services (AWS)
- AWS Elastic BeansTalk 에서는 아래 두가지 방법으로 배포할 수 있다.
    - Using the Tomcat Platform
        - war 파일을 사용해서 배포한다, 별다른 설정이 필요 없는 방법이다.
    - Using the Java SE Platform
        - Jar 파일을 사용해서 배포한다
        - Elastic BeansTalk 환경에서느 port 80 으로 nginx 인스턴스를 실행하여 포트 5000에서 실행되는 실제 어플리케이션으로 프록시 한다.
        - 아래 옵션 필요
        ```java
        server.port = 5000
        ```

## 3. Installing Spring Boot Applications
- java -java 명령어 이외에 fully executable applications for Unix systems도 가능하다.
- jar 파일을 실행하는 것이 아닌, init.d / systemd 에 등록하며 데몬으로 실행 시킬 수 있다.
- 해당 방법으로 배포하기 위해서는 먼저 아래와 같이 의존성 설정이 필요하다.
    - maven
   ```
        <plugin>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-maven-plugin</artifactId>
             <configuration>
                <executable>true</executable>
            </configuration>
        </plugin>
     ```
    - gradle
    ```
   bootJar {
        launchScript()
   }
   ```
  
- 위와 같이 의존성을 추가하였다면, 위에서 추가한 의존성에 의해 실핼 스크립트가 jar 안에 내장 된다.
- 즉, `./my-application.jar` 를 실행시켜 어플리케이션을 실행할 수 있다.
  

- /var/myapp에 init.d 서비스에 스프링 부트 어플리케이션을 설치하려면 아래와 같이 실행시켜야 한다.
```
$ sudo ln -s /var/myapp/myapp.jar /etc/init.d/myapp
```
- 설치가 완료되면 아래 명령어로 실행시킬 수 있다.
```
$ service myapp start
```
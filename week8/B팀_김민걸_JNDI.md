# Tomcat VS JBOSS
- Tomcat 은 J2EE 의 Servlet Container, Web Server 를 지원하는 웹서버
- JBOSS 는 J2EE 의 모든 기술을 완전히 구현한 구현체 서버. (Tomcat 의 기능 포함)

|기능|내용|TOMCAT|JBOSS|
|------|---|---|---|
|Servlet|JBOSS 는 톰캣을 내장|O|O|
|JNDI|Tomcat 은 읽기 전용|O|O|
|Database Connection Pooling| Tomcat 은 DBCP 이용 |X|O|
|JTP|Tomcat 은 별도 서비스 이용|X|O|
|JMX| |O|O|
|JMS|Tomcat 은 3rd party 라이브러리 사용|X|O|
|EJB|Tomcat 은 3rd party 라이브러리 사용|X|O|
|Hot Deploy| |O|O|
|WebService|Tomcat 은 3rd party 라이브러리 사용|X|O|


# JNDI

- 태초에 JDBC 는 코드에서 설정을 하였나니..
```java
Class.forName("com.mysql.cj.jdbc.Driver");
conn = DriverManager.getConnection(dburl,dbUser,dbpasswd);
```
- JNDI 는 ?
    - Java Naming and Directory Interface
    - 자바 네이밍, 디렉토리 인터페이스
    - 언제?
        - 데이터베이스의 서버 이름이라던지, 비밀번호라던지 바꿀 필요가 있어서 JDBC URL 을 변경할 필요가 있는 경우
        - DB 를 바꿔야 하는 경우
        - 등 나머지 뭐 DB 설정 (커넥션 풀 설정 등) 이 바뀔 필요가 있을 경우
    - 코드에서 바꾸지 말고, 어떠한 설정값으로 이를 조정하고 싶다 !
        - 어떠한 XML 설정으로 관리하자 !
        - 이미 떠있는 에플리케이션이라도 JBOSS 의 XML 설정만 다시 배포하면 바꿀 수 있다 !
        - 컴파일 새로 안해도 됨 !

## JNDI JBOSS 예시
- JBOSS J2EE XML 데이터소스 설정
 ```xml
<? Xml version = "1.0" encoding = "UTF-8"?> 
<datasources> 
<local-tx-datasource> 
<jndi-name> MySqlDS </ jndi-name> 
<connection-url> jdbc: mysql :/ / localhost: 3306/lw </ connection-url> 
<driver-class> com.mysql.jdbc.Driver </ driver-class> 
<user-name> root </ user-name> 
<password> rootpassword </ password> 
<exception-sorter-class-name> org.jboss.resource.adapter.jdbc.vendor.MySQLExceptionSorter </ exception-sorter-class-name> 
<metadata> 
<type-mapping> mySQL </ type-mapping> 
</ Metadata> 
</ Local-tx-datasource> 
</ Datasources>
```
- 그러면 아래와 같이 사용 가능
```java
    Context ctx = new InitialContext (); 
    Object datasourceRef = ctx.lookup ("java: MySqlDS");
    DataSource ds = (Datasource) datasourceRef;
    conn = ds.getConnection ();
        
    // lookup 이라는 메서드와 JNDI 이름으로 DataSource 를 얻어온다 !
```

## Spring boot ?
 ```
 spring:
  datasource:
    jndi-name: "java:jboss/datasources/customers"
 ```
- 위 설정을 하면, JNDI 로 DataSource 를 사용할 수 있다. (JBOSS 사용한다는 가정)
- JBoss 를 사용하게 되면, webapp/WEB-INF/jboss_web.xml 을 추가하게 되고, 그 안의 <Context> 에서 JNDI 설정을 한다.


- Embedded Tomcat 에는 JNDI 가 비활성화 되어 있다. 사용하려면 `Tomcat.enableNaming()` 을 호출해야 한다.
```java
@Bean
public TomcatEmbeddedServletContainerFactory tomcatFactory() {
    return new TomcatEmbeddedServletContainerFactory() {

        @Override
        protected TomcatEmbeddedServletContainer getTomcatEmbeddedServletContainer(
                Tomcat tomcat) {
            tomcat.enableNaming();
            return super.getTomcatEmbeddedServletContainer(tomcat);
        }
    };
}
```


## 실질적으로 언제 사용하나 ?
- DevOps 다 뭐다 하는 요즘초년생이라 잘 모르겠지만..
    - Dev 와 Ops 가 나눠져 있던 시절에는 설정과 같은 것들을 WAS 에서 설정하는게 편하다.
    - WAS 에 여러 애플리케이션이 실행중일때, 하나의 설정으로 모두를 관리하면 자원낭비를 줄일 수 있다.
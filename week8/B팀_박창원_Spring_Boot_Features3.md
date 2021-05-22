#  Working with SQL Databases

스프링 프레임워크는 SQL 데이터베이스를 다룰 수 있도록 광범위한 지원을 제공한다.

[Spring Data](https://spring.io/projects/spring-data)는 추가적인 SQL 기능을 제공한다.

`Repository` 를 인터페이스로부터 곧바로 구현체를 생성해준다. 그리고,  메서드 이름을 통해 쿼리를 자동생성 해준다.

## DataSource 설정하기

자바의 `javax.sql.DataSource` 인터페이스는 데이터베이스 연결을 위한 작업을 담당한다.

전통적으로, DataSource는 URL과 그 뒤에 계정 정보를 입력하여 데이터베이스 연결을 확립한다.

DataSource 설정 방법은 [the “How-to”](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/howto.html#howto-configure-a-datasource) 을 참고.

프로덕션 환경에서 데이터베이스 연결 또한 풀링 `DataSource` 라이브러리에 의해 자동설정 될 수 있다.

스프링 부트는 풀링 DataSource 선택을 위해 다음과 같은 알고리즘을 따른다.

1. [HikariCP](https://github.com/brettwooldridge/HikariCP) 가 성능과 동시성 측면에서 가장 선호된다.
2. HikariCP가 없다면, Tomcat pooling `DataSource` 가 사용된다.
3. 그게 아니라면, [Commons DBCP2](https://commons.apache.org/proper/commons-dbcp/) 를 사용한다.
4. 가장 마지막으로 Oracle UCP를 사용한다.

`spring-boot-starter-jdbc` 또는 `spring-boot-starter-data-jpa`가 의존성에 추가되었다면, HikariCP가 의존성으로 추가된다.

만약 DataSource 빈을 직접 등록한다면, DataSource 등록을 위한 자동설정이 발생하지 않는다.



DataSource를 등록하려면 다음 최소 설정 3가지를 application.properties에 입력하자.

```properties
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
```

- `spring.datasource.url` 프로퍼티를 설정하지 않으면, 스프링 부트는 내장형 DB를 자동 설정하려고 할 것이다.
- 스프링 부트는 대부분의 JDBC 드라이버 클래스를 URL로 부터 추론하여 자동 등록해준다. 만약 특정 클래스를 사용하려면, `spring.datasource.driver-class-name` 프로퍼티를 사용하라.



## Hikari VS Tomcat Jdbc 

Hikari CP와 Tomcat Jdbc의 메모리 사용량을 확인해보자.

테스트 코드는 100개의 배치 insert문을 수행한다.

최대 pool size는 10개로 동일하게 설정한다.

```java
package me.cwpark.sbsandbox.dbcp;

import static java.lang.String.*;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.UUID;

import javax.sql.DataSource;

import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import me.cwpark.sbsandbox.benchmark.BenchMarker;

@Slf4j
@Component
@RequiredArgsConstructor
public class DbcpTester implements ApplicationRunner {

	private final BenchMarker benchMarker;
	private final DataSource dataSource;

	@Override
	public void run(ApplicationArguments args) throws Exception {

		log.info("Datasource type ? {}", dataSource.getClass().getName());
		log.info("Before test: heap usage {}", benchMarker.getHeapUsage());
		insertMeetCoder();
		log.info("After test: heap usage {}", benchMarker.getHeapUsage());
	}

	private void insertMeetCoder() {
		try (Connection conn = dataSource.getConnection();
			 PreparedStatement ps = conn.prepareStatement(createStatement())) {
			for (int i = 0; i < 100; i++) {
				ps.addBatch(createStatement());
			}
			ps.executeBatch();
		} catch (SQLException e) {
			throw new RuntimeException(e);
		}
	}

	private String createStatement() {
		return format("insert into meetcoder(name) values('%s')", UUID.randomUUID().toString());
	}
}

```



출력 로그

```

2021-05-22 13:04:05.699  INFO 66183 --- [           main] me.cwpark.sbsandbox.dbcp.DbcpTester      : Datasource type ? com.zaxxer.hikari.HikariDataSource
2021-05-22 13:04:05.701  INFO 66183 --- [           main] me.cwpark.sbsandbox.dbcp.DbcpTester      : Before test: heap usage memory usage : 21.3642578125
2021-05-22 13:04:07.000  INFO 66183 --- [           main] me.cwpark.sbsandbox.dbcp.DbcpTester      : After test: heap usage memory usage : 44.0532672531
```



Hikari CP 메모리 사용량

| 시도 | insert 문장 수행 전 힙 사용량 | insert 문장 수행 후 힙 사용량 | 차이 값  |
| ---- | ----------------------------- | ----------------------------- | -------- |
| 1    | 19.92 mb                      | 41.92 mb                      | 22.00 mb |
| 2    | 21.46 mb                      | 44.05 mb                      | 22.60 mb |
| 3    | 18.74 mb                      | 41.26 mb                      | 22.52 mb |
| 4    | 20.96 mb                      | 45.96 mb                      | 25.00 mb |
| 5    | 19.92 mb                      | 42.92 mb                      | 23.00 mb |



Tomcat Jdbc 사용시 메모리 사용량

| 시도 | insert 문장 수행 전 힙 사용량 | insert 문장 수행 후 힙 사용량 | 차이 값  |
| ---- | ----------------------------- | ----------------------------- | -------- |
| 1    | 15.43 mb                      | 44.92 mb                      | 29.49 mb |
| 2    | 21.46 mb                      | 49.96 mb                      | 28.51 mb |
| 3    | 21.47 mb                      | 49.97 mb                      | 28.50 mb |
| 4    | 19.20 mb                      | 48.07 mb                      | 28.87 mb |
| 5    | 21.44 mb                      | 49.94 mb                      | 28.50 mb |



메모리 효율 면에서 HikariCP가 Tomcat JDBC에 비해 우수한 것을 확인할 수 있다.




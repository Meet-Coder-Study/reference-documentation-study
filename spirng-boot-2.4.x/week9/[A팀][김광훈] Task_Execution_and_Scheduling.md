# Task Execution and Scheduling
## 1. Executor
#### (1) Executor 란?
<img width="700" src="https://user-images.githubusercontent.com/60383031/120881528-b3992f00-c60c-11eb-8e28-f31972f3db54.png">

- Java 5에 도입되었으며, 단순히 `void execute(Runnable command);` 메서드만 정의된 객체이다.
- command 를 실행할 때 마다 새로운 스레드에 동작
- command 는 Runnable 인터페이스 객체이다.

<br>
<img width="700" src="https://user-images.githubusercontent.com/60383031/120881667-b5172700-c60d-11eb-8f50-a42d820681ed.png">

- Executor 가 실행될 때는 쓰레드를 명시하지 않는다고 한다. 그 역할은 Runnable 인터페이스가 대신한다.
- `executor.execute(new RunnableTask());` 이런 식으로 command 를 실행 시킨다.

<br>

#### (2) Runnable Interface
  <img width="700" src="https://user-images.githubusercontent.com/60383031/120881749-628a3a80-c60e-11eb-925a-6a31616cfbde.png">

- 자바에서는 Thread 를 구현할 때 Thread 클래스를 사용하거나 Runnable 인터페이스를 사용할 수 있다.
- 위 코드는 Runnable 인터페이스의 코드이며, 주석을 살펴보면 Runnable 인터페이스의 구현체가 create a thread 를 할 때, 구현체의 run 메서드를 실행시킨다.


## 2. ThreadPoolTaskExecutor
#### (1) ThreadPoolTaskExecutor 란 ?
- Spring 에서 쓰레드를 관리해주는 역할을 하며, Executor 를 최상위 인터페이스로 가지고 있다.
- 자바의 ThreadPoolExecutor 를 사용하기 쉽게 만들어 사용할 수 있도록 해주는 일종의 Wrapper 클래스라고 보면 편하다.
- 코드

<br>
<img width="700" src="https://user-images.githubusercontent.com/60383031/120919690-dfdcaa80-c6f5-11eb-8747-72d049a128df.png">


- 상속 관계

<br>
<img width="700" src="https://user-images.githubusercontent.com/60383031/120882020-10e2af80-c610-11eb-95c9-2c34755612bd.png">

- ThreadPoolTaskExecutor 클래스는 `AsyncListenableTaskExecutor`, `SchedulingTaskExecutor` 인터페이스를 상속 받고 있는 것을 볼 수 있다.
- 두 인터페이스 모두 `AsyncTaskExecutor` 인터페이스를 extends 하고 있다.
- 그리고 AsyncTaskExecutor 인터페이스는 Executor 인터페이스를 exntends 하고 있는 TaskExecutor 인터페이스를 extends 하고 있다.

<br>

#### (2) AsyncListenableTaskExecutor
<img width="700" src="https://user-images.githubusercontent.com/60383031/120919810-7741fd80-c6f6-11eb-9938-7d1be5ff58fa.png">

- add submit 할 때, ListenableFutures 기눙을 추가하기 위해 만들어진 인터페이스이다.
- Runnable 인터페이스 객체인 task 를 던지는 것을 볼 수 있다.

<br>

#### (3) SchedulingTaskExecutor
<img width="700" src="https://user-images.githubusercontent.com/60383031/120882632-05918300-c614-11eb-865e-edc4ece42fbd.png">

- 해당 인터페이스는 힌트 (플래그)로만 사용된다 ??
- 반복된 루프를 개별 하위 작업으로 분할하여 이후 후속 작업을 submit 할 수 있다.

<br>

#### (4) AsyncTaskExecutor
- TaskExecutor 를 상속한 인터페이스이다.
- submit 메서드와 timeout 기능이 추가되었다.

<img width="700" src="https://user-images.githubusercontent.com/60383031/120883255-29a29380-c617-11eb-91a7-86dcc8f68f51.png">

- 스프링에서는 `@EnableAsync` 어노테이션을 추가하면 디폴트로 `SimpleAsyncTaskExecutor` 구현체를 사용한다.
- SimpleAsyncTaskExecutor 객체는 매번 Thread 를 생성하는 객체이기 때문에 쓰레드 풀내에서 지정된 갯수 만큼만 실행 시키고 싶다면 아래와 같이 ThreadPool 설정이 필요하다.
- 사용 예제 (@EnableAsync 주석 예제)
```java
@Configuration
@EnableAsync
public class AsyncConfig extends AsyncConfigurerSupport {

  @Override
  public Executor getAsyncExecutor() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(7);
    executor.setMaxPoolSize(42);
    executor.setQueueCapacity(11);
    executor.setThreadNamePrefix("MyExecutor-");
    executor.initialize();
    return executor;
  }
}
```
- 스프링 부트에서는 아래와 같이 프로퍼티로 ThreadPoolTaskExecutor 를 설정할 수 있다.
- 왜냐하면 `Executor` 객체가 Bean으로 등록이 되지 않았다면 `ThreadPoolTaskExecutor` 객체를 auto-configure 하기 때문이다.
```java
spring.task.execution.pool.max-size=16
spring.task.execution.pool.queue-capacity=100
spring.task.execution.pool.keep-alive=10s
```

<br>

#### 참고
- https://github.com/HomoEfficio/dev-tips/blob/master/Java-Spring%20Thread%20Programming%20%EA%B0%84%EB%8B%A8%20%EC%A0%95%EB%A6%AC.md
- https://brunch.co.kr/@springboot/401  
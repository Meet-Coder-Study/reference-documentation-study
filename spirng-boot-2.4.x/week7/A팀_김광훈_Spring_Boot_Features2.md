# SpringBootFeatures2
## Graceful shutdown
사용하는 이유
```
- 애플리케이션이 Kill 메시지를 수신했을 때 바로 종료 된다면, 현재 처리되고 있는 작업들은 완료되지 않은 상태로 서버가 내려가게 된다.
- 해당 옵션을 사용하면 현재 처리되고 있는 작업을 모두 종료 후에 안전하게 어플리케이션을 종료시킬 수 있다.
- 정리하자면 Kill 명령어 수신 이후에 들어오는 요청은 막고, 이전에 들어오는 요청은 안전하게 처리하고 종료
- 추가로 WAS 마다 약간 상이하다.
    -  Tomcat, Netty 및 Jetty는 네트워크 계층에서 새 요청 수락을 중지합니다. 반면 Undertow는 새로운 요청을 계속 수락하지만 즉시 503 Service Unavailable 응답을 클라이언트에 보냅니다
```

설정
- enable graceful shutdown (새로운 요청을 받지 않음)
```
server.shutdown=graceful
```

- configure the timeout period (디폴트 30초, 최대 설정은 1분까지)
```
spring.lifecycle.timeout-per-shutdown-phase=20s
```

## Calling REST Services with RestTemplate
개념
```
- 애플리케이션에서 외부 API를 Call 하는 경우에 사용
- Spring Boot는 RestTemplate 빈을 제공하지 않음  
- 필요할때마다 인스턴스를 만들 수 있게 builder 제공
- 참고: https://bravenamme.github.io/2020/10/06/graceful-shutdown/
```

예제
```java
@Service
public class MyService {

    private final RestTemplate restTemplate;

    public MyService(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }

    public Details someRestCall(String name) {
        return this.restTemplate.getForObject("/{name}/details", Details.class, name);
    }
}
```

Custom RestTemplate
```java
static class ProxyCustomizer implements RestTemplateCustomizer {

    @Override
    public void customize(RestTemplate restTemplate) {
        HttpHost proxy = new HttpHost("proxy.example.com");
        HttpClient httpClient = HttpClientBuilder.create().setRoutePlanner(new DefaultProxyRoutePlanner(proxy) {

            @Override
            public HttpHost determineProxy(HttpHost target, HttpRequest request, HttpContext context)
                    throws HttpException {
                if (target.getHostName().equals("192.168.0.5")) {
                    return null;
                }
                return super.determineProxy(target, request, context);
            }

        }).build();
        restTemplate.setRequestFactory(new HttpComponentsClientHttpRequestFactory(httpClient));
    }
}
```

## WebClient
개념
- 장점: this client has a more functional feel and is fully reactive
- 동기/비동기 모두 지원하기 때문에 RestTemplate 대체가 가능하다.  
- WebClient 를 사용하려면 Spring WebFlux 의존성이 추가 되어 있어야 한다.
- 

WebClient vs RestTemplate
- 과거 스프링 버전에서는 RestTemplate 을 deprecated 할 예정이라고 선엄함
  

  <img width="700" src="https://user-images.githubusercontent.com/60383031/118957116-35495580-b99b-11eb-8164-5093cd6be88d.png">

- 현재(Spring boot 2.4.5) 에서는 유지보수 진행 중이라고 입장을 바꿧지만, 그래도 WebClient를 사용할 것을 권장하고 있다.
  

  <img width="700" src="https://user-images.githubusercontent.com/60383031/118957354-6b86d500-b99b-11eb-8165-df778251d6c0.png">


에제
```java
@Service
public class MyService {

    private final WebClient webClient;

    public MyService(WebClient.Builder webClientBuilder) {
        this.webClient = webClientBuilder.baseUrl("https://example.org").build();
    }

    public Mono<Details> someRestCall(String name) {
        return this.webClient.get().uri("/{name}/details", name)
                        .retrieve().bodyToMono(Details.class);
    }
}
```









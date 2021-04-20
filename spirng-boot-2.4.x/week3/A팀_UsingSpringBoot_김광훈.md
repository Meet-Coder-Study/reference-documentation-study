# Using Spring Boot 

## Build Systems
- 2장 내용 참고
- grable flow


  <img width="300" src="https://user-images.githubusercontent.com/60383031/115390641-7114c200-a219-11eb-8501-d01afafa5a9f.png">


## Configuration Classes
```
- Spring 에서는 XML 방식으로도 할 수 있지만 Spring boot에서는 애노테이션으로 간단하게 할 수 있음
- 과거: 스프링 (출처 Spring in Action)
    - XML example
        <bean id="inventoryService"
              class="com.example.InventoryService" />
        
        <bean id="productService"
              class="com.example.ProductService" />
          <constructor-agr ref="inventoryService" />   
        </bean>
        
- 현재: 스프링 부트 (@Configuration)
    - 각 Bean을 스프링 애플리케이션 컨텍스트에 제공하는 구성 클래스라는 것을 스프링에게 전달
    - 구성 클래스의 메서드에는 @Bean 애노테이션이 저장되어 있음 
    - Annotation style example
        @Configuration
        public class ServiceConfiguration {
            @Bean
            public InventoryService inventoryService() {
                return new InventoryService();
            }
            
            @Bean
            public ProductService productService() {
                return new ProductService(inventoryService());
            }
        }

```

## Auto-configuration
```
- 실행 가능한 JAR 파일에서 애프리케이션을 실행하므로 제일 먼저 시작되는 클래스가 존재해야 한다.
- @SpringBootApplication
    - (1) @SpringBootConfiguration: 현재 클래스를 구성 클래스로 지정 
    - (2) @EnableAutoConfiguration: 스프링 부트 자동 구성을 활성화 시킨다.
    - (3) @ComponentScan: 컴포넌트 스캔을 확성화 한다. --> @Component 애노테이션이 붙은 클래스를 스캔해서 스프링 Bean 으로 등록 --> @Configuration 소스코드에도 @Component 어노테이션이 들어 있다.
```
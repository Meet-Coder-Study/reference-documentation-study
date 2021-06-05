# 9week NoSQL Technologies


https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-nosql

## Working with NoSQL Technologies

몽고디비도!! querydsl을 쓸수 있다.

아래럼 기본 적인 셋팅을 하고
```java
spring:
  data:
    mongodb:
      uri:
      database:
```


```gradle
        implementation 'org.springframework.boot:spring-boot-starter-data-mongodb'
        compile("com.querydsl:querydsl-mongodb:$querydsl_version")
        compile("com.querydsl:querydsl-apt:${querydsl_version}")
```

```java

@QueryEntity
@Document(collection = "user")
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserCollection {

  @Id @Field private String id;

  @Field private String userId;

  @Field private String userName;

  @Field private String email;

  @Field private String address;

  @Field private Integer age;

  @Field @CreatedBy private String createBy;
  @Field @CreatedDate private LocalDateTime createdAt;
  @Field @LastModifiedBy private String updatedBy;
  @Field @LastModifiedDate private LocalDateTime updatedAt;

  @Version
  private Integer version;

  @Field("isDeleted")
  private int isDeleted;
}
```

Qclass generate

```gradle

def querydslSrcDir = 'src/main/generated'

querydsl {
    springDataMongo = true
    library = "com.querydsl:querydsl-apt"
//    jpa = true
    querydslSourcesDir = querydslSrcDir
}

compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    querydsl.extendsFrom compileClasspath
}

sourceSets {
    main {
        java {
            srcDirs = ['src/main/java', querydslSrcDir]
        }
    }
}


task deleteGeneratedSources(type: Delete) {
    delete file(querydslSrcDir)
}

tasks.withType(JavaCompile) { it.dependsOn('deleteGeneratedSources') }
//
project.afterEvaluate {
    project.tasks.compileQuerydsl.options.compilerArgs = [
            "-proc:only",
            "-processor", project.querydsl.processors() +
                    ',lombok.launch.AnnotationProcessorHider$AnnotationProcessor'
    ]
}
```


몽고디비를 쓸경우에도 다이나믹 쿼리를 만들어 사용이 가능하다

```java
public class UserPredictor {

  private static final QUserCollection userCollection = QUserCollection.userCollection;
  private BooleanBuilder builder = new BooleanBuilder();

  public UserPredictor userId(String userId) {

    if (!ObjectUtils.isEmpty(userId)) {

      builder.and(userCollection.userId.eq(userId));
    }
    return this;
  }

  public UserPredictor email(String email) {

    if (!ObjectUtils.isEmpty(email)) {

      builder.and(userCollection.email.eq(email));
    }
    return this;
  }

  public UserPredictor userName(String userName) {

    if (!ObjectUtils.isEmpty(userName)) {

      builder.and(userCollection.userName.eq(userName));
    }
    return this;
  }

  public Predicate values() {
    return builder.getValue();
  }
}
```


몽고디비를 쓰면서 주의해야할점

4버전 이상부터는 트랜잭션이 보장
4버전 미만에서는 트랜잭션이 보장되지 않는다.
몽고 템플릿을 사용시 allowdisk true를 쓰게 될 경우 추후 장애가 발생할수 있음을 명심한다.

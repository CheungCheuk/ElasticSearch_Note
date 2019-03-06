

[TOC]



------

### No property index found for type Good!

```java
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'commentService': 
Unsatisfied dependency expressed through field 'goodService'; 

nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: 
Error creating bean with name 'goodService': Unsatisfied dependency expressed through field 'goodRepository'; 

nested exception is org.springframework.beans.factory.BeanCreationException: 
Error creating bean with name 'goodRepository': Invocation of init method failed; 

nested exception is java.lang.IllegalArgumentException: 
Failed to create query method public abstract java.lang.Object org.springframework.data.elasticsearch.repository.ElasticsearchRepository.index(java.lang.Object)! 
    
No property index found for type Good!
```

#### 原因

- JPA 代理了 Elasticsearch Repository

#### 解决

- 指定 JPA 需要扫描的包

```java
@SpringBootApplication
@EnableJpaRepositories({"com.cheung.emall.dao","com.cheung.emall.pojo"})
public class BootApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootApplication.class,args);
    }
}
```

[^Reference ]:[spring boot elasticsearch集成过程错误记录 - 花树堆雪的个人空间 - 开源中国](https://my.oschina.net/u/1428688/blog/2988104)

------

### Error creating bean with name 'elasticsearchClient' defined in class path resource

- java.lang.NoClassDefFoundError: org/springframework/data/util/Streamable

```java
 Cannot resolve reference to bean 'elasticsearchTemplate' while setting bean property 'elasticsearchOperations'; 

nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: 
Error creating bean with name 'elasticsearchTemplate' defined in class path resource [org/springframework/boot/autoconfigure/data/elasticsearch/ElasticsearchDataAutoConfiguration.class]: Unsatisfied dependency expressed through method 'elasticsearchTemplate' parameter 0; 

nested exception is org.springframework.beans.factory.BeanCreationException: 
Error creating bean with name 'elasticsearchClient' defined in class path resource [org/springframework/boot/autoconfigure/data/elasticsearch/ElasticsearchAutoConfiguration.class]: Bean instantiation via factory method failed; 

nested exception is org.springframework.beans.BeanInstantiationException: 
Failed to instantiate [org.elasticsearch.client.Client]: 

Factory method 'elasticsearchClient' threw exception; 

nested exception is java.lang.NoClassDefFoundError: org/springframework/data/util/Streamable
at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:588) ~[spring-beans-4.3.13.RELEASE.jar:4.3.13.RELEASE]
```

#### 解决

- 引入最新版本：2.1.5 RELEASE

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-commons</artifactId>
    <version>2.1.5.RELEASE</version>
</dependency>
```

[Errors when using spring-data-elasticsearch version 3.0.0.BUILD-SNAPSHOT and Elasticsearch 5.4.0 - Elasticsearch - Discuss the Elastic Stack](https://discuss.elastic.co/t/errors-when-using-spring-data-elasticsearch-version-3-0-0-build-snapshot-and-elasticsearch-5-4-0/90525/6)

[Streamable (Spring Data Core 2.1.5.RELEASE API)](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/util/Streamable.html)

[Maven Repository: org.springframework.data » spring-data-commons » 2.1.5.RELEASE](https://mvnrepository.com/artifact/org.springframework.data/spring-data-commons/2.1.5.RELEASE)

------



### BootstrapMethodError：NoClassDefFoundError

- java.lang.NoClassDefFoundError: org/springframework/core/ `ReactiveTypeDescriptor`

```json
2019-03-06 12:35:00.752 ERROR 65088 --- [  restartedMain] o.s.boot.SpringApplication               : Application startup failed

java.lang.BootstrapMethodError: java.lang.NoClassDefFoundError: org/springframework/core/ReactiveTypeDescriptor

at org.springframework.data.repository.util.ReactiveWrappers.findDescriptor(ReactiveWrappers.java:281) ~[spring-data-commons-2.1.5.RELEASE.jar:2.1.5.RELEASE]
```

[^Spring 5.0 API]:[ReactiveTypeDescriptor (Spring Framework 5.1.5.RELEASE API)](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/ReactiveTypeDescriptor.html)
[^spring 和 Springboot 联系]:[A Comparison Between Spring and Spring Boot | Baeldung](https://www.baeldung.com/spring-vs-spring-boot)

[java.lang.NoClassDefFoundError in spring data cassandra project - Stack Overflow](https://stackoverflow.com/questions/48770959/java-lang-noclassdeffounderror-in-spring-data-cassandra-project)

------



[^使用 springboot 2.0 后，需要将 Redis 配置更改]:[java - What is the difference between RedisCacheWriter and RedisTemplate - Stack Overflow](https://stackoverflow.com/questions/53335003/what-is-the-difference-between-rediscachewriter-and-redistemplate)

------

### Failed to bind properties under 'spring.servlet.multipart.max-file-size' to org.springframework.util.unit.DataSize:

[Springboot文件上传报错：failed to convert java.lang.String to org.springframework.util.unit.DataSize - xqnode的博客 - CSDN博客](https://blog.csdn.net/xqnode/article/details/86558874)
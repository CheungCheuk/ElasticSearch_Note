[TOC]

------

## Version & Compatibility

### `Client` 和 `Server` 版本一致

- 查看服务器版本：终端输入

```bash
 elasticsearch -V
```

- 结果：6.5.4 版本

```json
Version: 6.5.4, Build: oss/tar/d2ef93d/2018-12-17T21:17:40.758843Z, JVM: 1.8.0_191
```

- 版本兼容性对照

| Elasticsearch | RestClient | Spring data Elasticsearch |
| ------------- | ---------- | ------------------------- |
| 6.5.4         | 6.5.4      | 3.2.x                     |

[^spring data 对各版本 Reference 指引]:[Spring Data Elasticsearch](https://spring.io/projects/spring-data-elasticsearch#learn)

[^spring data 版本 和 Elasticsearch 版本对照表]:[spring-projects/spring-data-elasticsearch: Spring Data Elasticsearch](https://github.com/spring-projects/spring-data-elasticsearch)

[^ spring data Elasticsearch 3.2 Reference]:[Spring Data Elasticsearch - Reference Documentation](https://docs.spring.io/spring-data/elasticsearch/docs/3.2.0.M1/reference/html/#project)
[^关于 Client 和 Cluster 版本兼容]:[Client](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/client.html)



------

## Maven Dependency

- 使用 `RestHighLevelClient`，保证后续 Elasticsearch 版本的兼容性
- 这里使用 `Elasticsearch 6.5.4` 版本，根据实际需要，进行替换
- 原则上应该用 `spring-data-elasticsearch 3.2.x` 版本支持 `Elasticsearch 6.5.4` 版本 ，但是此刻还没发布，所以使用 `3.1.5.RELEASE` 版本

```xml
  <!-- Elasticsearch Client-->
  <dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>6.5.4</version>
  </dependency>

  <dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>6.5.4</version>
  </dependency>

  <dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-client</artifactId>
    <version>6.5.4</version>
  </dependency>
  <dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-client-sniffer</artifactId>
    <version>6.5.4</version>
  </dependency>

<!-- Spring data Elasticsearch -->
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-elasticsearch</artifactId>
    <version>3.1.5.RELEASE</version>
</dependency>

```

[^ 旧客户端往后兼容，但新客户端无法往前兼容]:[Compatibility | Java REST Client [6.6] | Elastic ](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-compatibility.html)

## Config

### application.properties

```properties
spring.data.elasticsearch.cluster-name=elasticsearch
spring.data.elasticsearch.cluster-nodes=elasticsearch
```

### Java config class

- 使用 `Rest Client API` 连接 `Elasticsearch server`

```java
package com.journaldev.elasticsearch.config;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.beans.factory.config.AbstractFactoryBean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ElasticSearchConfiguration extends AbstractFactoryBean {

    private static final Logger LOG = LoggerFactory.getLogger(ElasticSearchConfiguration.class);
    @Value("${spring.data.elasticsearch.cluster-nodes}")
    private String clusterNodes;
    @Value("${spring.data.elasticsearch.cluster-name}")
    private String clusterName;
    private RestHighLevelClient restHighLevelClient;

    @Override
    public void destroy() {
        try {
            if (restHighLevelClient != null) {
                restHighLevelClient.close();
            }
        } catch (final Exception e) {
            LOG.error("Error closing ElasticSearch client: ", e);
        }
    }

    @Override
    public Class<RestHighLevelClient> getObjectType() {
        return RestHighLevelClient.class;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }

    @Override
    public RestHighLevelClient createInstance() {
        return buildClient();
    }

    private RestHighLevelClient buildClient() {
        try {
            restHighLevelClient = new RestHighLevelClient(
                    RestClient.builder(
                            new HttpHost("localhost", 9200, "http"),
                            new HttpHost("localhost", 9201, "http")));
        } catch (Exception e) {
            LOG.error(e.getMessage());
        }
        return restHighLevelClient;
    }
}
```

- [^关于配置 client 连接]:[Spring Boot Elasticsearch - JournalDev](https://www.journaldev.com/18148/spring-boot-elasticsearch)

- [^关于 Java client 选择]:[Elasticsearch Java Clients | Elastic](https://www.elastic.co/blog/state-of-the-official-elasticsearch-java-clients)

------

## Query

### Spring 和 Elasticsearch 交互方式

#### 使用 Repository

- 包括 `CrudRepository` 和 `PagingAndSortingRepository` 的功能
- Spring data 根据接口的方法名，自动创建代理
- 可以通过注释 `@Query` 自定义原生 JSON 查询

#### 使用 Template

- Spring data 在 Elasticsearch Java client 上 完全实现了抽象层
- 类似于 `RedisTemplate` 在 Spring data 中的作用

[^Spring data 支持 Elasticsearch 原理]:[Spring Data Elasticsearch](https://www.elastic.co/blog/found-java-clients-for-elasticsearch#spring-data-elasticsearch)
[^使用 Java High Level REST Client API 进行搜索]:[Search Template API](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.6/java-rest-high-search-template.html)
[^查询方式由 Transport Client 转换为 RestHighLevelClient ]:[Changing the application’s code](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.6/_changing_the_application_8217_s_code.html)



```

```





```

```


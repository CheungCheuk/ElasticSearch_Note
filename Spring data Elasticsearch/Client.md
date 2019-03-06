[TOC]



------

## Java Client

### 通信方式

#### 通过 HTTP 使用 REST API

- node 将 JSON 解析，并转换为相应的 java 对象
- 在 cluster 的其他 node 之间，使用二进制格式传输数据

#### 使用 Elasticsearch 内置 Java  API

- 使用网络 **运输层**  `transport layer network` 通信

- `node - node` 方式

- 使用 `transport client` 直接在 **应用层** 构建 java 对象

- 通过 **运输层**，发送相同的二进制格式数据

- 理论上，Java API 表现优于 REST，因为可以跳过 REST 方式的解析 `parsing` 步骤

- 但其实 HTTP client 性能差异和 Transport client 对比可以忽略

    >  [Benchmarks, however,  show](https://www.elastic.co/blog/benchmarking-rest-client-transport-client) that the performance of the HTTP client is close enough to that of the Transport client that the difference can be pretty much disregarded.

####  Java API 对比 REST API 缺陷

|                  | Java API                                                | REST API                                              |
| ---------------- | ------------------------------------------------------- | ----------------------------------------------------- |
| **向后兼容性**   | 更新所有的 `cluster`、`node` 和调用 Java API 编写的应用 | `Elasticsearch` 更新后更稳定、可以跳过 `cluster` 更新 |
| **JVM** **版本** | `node`、`client` 建议使用相同的 JVM 版本                | 可以使用应用的 JVM 版本                               |
| **依赖**         | `classpath` 的 `library` 冲突                           | 使用很少的依赖                                        |
| **安全**         | 只能通过 **传输层** 进行 `node-2-node` 通信             | 通过 `https` 加密                                     |

[^Reference]:[Elasticsearch Java Clients | Elastic](https://www.elastic.co/blog/state-of-the-official-elasticsearch-java-clients)

------



### Transport Client（将被抛弃）

- 通过 transport 模块连接 Elasticsearch cluster
- 不加入 cluster
- 初始化一个或多个 transport 地址，用来通信
- 开启嗅探功能(sniff)：动态添加新的 `host`，移除旧的 `host`

```java

//	cluster name 如果不是 “elasticsearch，则需要手动设置 cluster name
@Value("{elasticsearch.cluster.name:elasticsearch}")
private String clusterName;

Settings settings 
	= Settings.builder()
        .put("cluster.name", "myClusterName")
    	.put("client.transport.sniff", true)	//	开启嗅探功能
    	.build();
TransportClient client = new PreBuiltTransportClient(settings);



// on startup

TransportClient client 
	= new PreBuiltTransportClient(Settings.EMPTY)
    //	addTransportAddress：transport client 连接内部的 node
        .addTransportAddress(new TransportAddress(InetAddress.getByName("host1"), 9300))
        .addTransportAddress(new TransportAddress(InetAddress.getByName("host2"), 9300));	

// on shutdown

client.close();
```

- [^[Transport Client](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html)]:运输客户端
- [^[PreBuiltTransportClient (transport 6.6.1 API)](https://artifacts.elastic.co/javadoc/org/elasticsearch/client/transport/6.6.1/org/elasticsearch/transport/client/PreBuiltTransportClient.html)]:需要先安装 `Netty4Plugins` `ReindexPlugin` `PercolatorPlugin` `MustachePlugin` `ParentJoinPlugin`。**从 Elasticsearch 7.0 开始不再推荐，8.0 开始将移除。**使用 `High Level REST Client` 替代。 



#### TransportClient 和 RestHighLevelClient 对照

|                     | TransportClient                                              | RestHighLevelClient                                          |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **request builder** | `transportClient.prepareIndex()`                             | `new IndexRequest()`                                         |
|                     | `transportClient.prepareDelete()`                            |                                                              |
|                     | `transportClient.prepareSearch()`                            |                                                              |
| **同步执行**        | `transportClient.index(indexRequest).actionGet()`            | `client.index()`                                             |
|                     | `transportClient.delete(request).actionGet()`                | `client.delete(request, RequestOptions.DEFAULT);`            |
| **异步执行方法**    |                                                              | `client.indexAsync()`                                        |
|                     | `transportClient.delete(request, new ActionListener<DeleteResponse>() ` | `client.deleteAsync(request, RequestOptions.DEFAULT, new ActionListener<DeleteResponse>()` |

[^Reference]:[Changing the application’s code](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.6/_changing_the_application_8217_s_code.html)



------

### High Level Rest Client（推荐使用）

- 在 `Low Level REST Client` 工作
- 暴露指定方法的 API
- 每个 API 可以被 **同步** 或者 **异步** 调用
- 保证可以和任何 `Elasticsearch node` 进行通讯，包括任何版本
- 建议：当升级 `Elasticsearch cluster` 时，同时升级 `High Level Rest Client`，保证最新

[^Reference]:[Java High Level REST Client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high.html)
[^将 Transport Client 迁移到 RestHighLevelClient]:[How to migrate](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.6/_how_to_migrate.html)



#### Maven dependency

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>6.6.1</version>
</dependency>
```

[^maven 仓库]:[Maven Repository](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-getting-started-maven.html)
[^client 依赖]:[Dependencies](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-getting-started-dependencies.html)

#### 初始化、关闭

- 使用 `low-level client` 处理请求
- 由 `low-level client` 维护连接池

```java
RestHighLevelClient client = new RestHighLevelClient(
        RestClient.builder(
                new HttpHost("localhost", 9200, "http"),
                new HttpHost("localhost", 9201, "http")));
```

- 释放资源

```java
client.close();
```

#### RquestOpitons

- 所有 `RestHighLevelClient` API 接收 `RquestOpitons`处理请求
- 自定义请求选项：[low level client documentation](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-low-usage-requests.html#java-rest-low-usage-request-options)
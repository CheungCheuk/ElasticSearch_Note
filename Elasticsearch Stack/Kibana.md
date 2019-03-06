## 目录结构

| 目录     | 描述                       | 默认位置                |
| -------- | -------------------------- | ----------------------- |
| home     | `$KIBANA_HOME`             | 解压位置                |
| bin      | 启动服务器、插件           | `$KIBANA_HOME\bin`      |
| config   | 配置文件、`kibana.yml`     | `$KIBANA_HOME\config`   |
| data     | 被插件和 kibana 读取的数据 | `$KIBANA_HOME\data`     |
| optimize |                            | `$KIBANA_HOME\optimize` |
| plugins  | 插件                       | `$KIBANA_HOME\plugins`  |

[Install Kibana with .tar.gz | Kibana User Guide [6.6] | Elastic](https://www.elastic.co/guide/en/kibana/current/targz.html)





##### Kibana 可视化

```http
http://localhost:5601/app/kibana#/management/kibana/index?_g=()
```



------



###### 参考资料

- 下载、配置、启动：[Download Kibana Free • Get Started Now | Elastic](https://www.elastic.co/downloads/kibana)
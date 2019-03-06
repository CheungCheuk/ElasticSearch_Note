[TOC]



## 含义

- 全文本查询
- 在 `全文本域` 进行全文本查询



| 查询                | 作用                                                         |
| ------------------- | ------------------------------------------------------------ |
| match               | 全文本标准查询                                               |
| match_query         | 准确查询一个 短语 或 单词                                    |
| match_query_prefix  | 匹配 单词 或 短语 的前缀，使用最后的单词 通配符              |
| mulit_match         | 多个 field 进行 match 查询                                   |
| common              | 将 不常见的单词 优先级 提高为 常见                           |
| query_string        | 支持紧凑的 Lucene 查询语法，允许指定 AND、OR、NOT 条件，用一个查询字符串查询多个 field |
| simple_query_string | query_string 语法的简化版                                    |


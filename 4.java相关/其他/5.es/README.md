# 《Elasticsearch 核心技术与实战》
- 原Github 地址 https://github.com/onebirdrocks/geektime-ELK/
- 极客时间：《Elasticsearch核心技术与实战》视频课程购买地址 - https://time.geekbang.org/course/intro/197
## 第一部分：初识 Elasticsearch
### 第 1 章：概述
1. [课程介绍](part-1/1.1-课程介绍/README.md)
2. [课程综述及学习建议](part-1/1.2-课程综述及学习建议/README.md)
3. [Elasticsearch 简介及其发展历史](part-1/1.3-Elasticsearch简介及其发展历史/README.md)
4. [Elastic Stack 家族成员及其应用场景](part-1/1.4-ElasticStack家族成员及其应用场景/README.md)
### 第 2 章：安装上手
1. [Elasticsearch 的安装与简单配置](part-1/2.1-Elasticsearch的安装与简单配置/README.md)
2. [Kibana 的安装与界面快速浏览](part-1/2.2-Kibana的安装与界面快速浏览/README.md)
3. [在 Docker 容器中运行 Elasticsearch，Kibana 和 Cerebro](part-1/2.3-在Docker容器中运行Elasticsearch,Kibana和Cerebro/README.md)
4. [Logstash 安装与导入数据](part-1/2.4-Logstash安装与导入数据/README.md)
### 第 3 章：Elasticsearch 入门
1. [基本概念（1）：索引，文档和 REST API](part-1/3.1-基本概念1索引文档和RESTAPI/README.md)
2. [基本概念（2）：节点，集群，分片及副本](part-1/3.2-基本概念2节点,集群,分片及副本/README.md)
3. [文档的基本 CRUD 与批量操作](part-1/3.3-文档的基本CRUD与批量操作/README.md)
4. [倒排索引入门](part-1/3.4-倒排索引入门/README.md)
5. [通过分析器进行分词](part-1/3.5-通过分析器进行分词/README.md)
6. [Search API 概览](part-1/3.6-SearchAPI概览/README.md)
7. [URI Search 详解](part-1/3.7-URISearch详解/README.md)
8. [Request Body 与 Query DSL 简介](part-1/3.8-RequestBody与QueryDSL简介/README.md)
9. [Query String & Simple Query String 查询](part-1/3.9-QueryString&SimpleQueryString查询/README.md)
10. [Dynamic Mapping 和常见字段类型](part-1/3.10-DynamicMapping和常见字段类型/README.md)
11. [显式 Mapping 设置与常见参数介绍](part-1/3.11-显式Mapping设置与常见参数介绍/README.md)
12. [多字段特性及 Mapping 中配置自定义 Analyzer](part-1/3.12-多字段特性及Mapping中配置自定义Analyzer/README.md)
13. [Index Template 和 Dynamic Template](part-1/3.13-IndexTemplate和DynamicTemplate/README.md)
14. [Elasticsearch 聚合分析简介](part-1/3.14-Elasticsearch聚合分析简介/README.md)
15. 第一部分总结
## 第二部分：深入了解 Elasticsearch
### 第 4 章：深入搜索
1. [基于词项和基于全文的搜索](part-2/4.1-基于词项和基于全文的搜索/README.md)
2. [结构化搜索](part-2/44.2-结构化搜索/README.md)
3. [搜索的相关性算分](part-2/4.3-搜索的相关性算分/README.md)
4. [Query & Filtering 与多字符串多字段查询](part-2/4.4-Query&Filtering实现多字符串多字段查询/README.md)
5. [单字符串多字段查询：Dis Max Query](part-2/4.5-单字符串多字段查询-DisMaxQuery/README.md)
6. [单字符串多字段查询：Multi Match](part-2/4.6-单字符串多字段查询-Multi-Match/README.md)
7. [多语言及中文分词与检索](part-2/4.7-多语言及中文分词与检索/README.md)
8. [Space Jam，一次全文搜索的实例](part-2/4.8-SpaceJam一个全文搜索的实例/README.md)
9. [使用 Search Template 和 Index Alias 查询](part-2/4.9-使用SearchTemplate和IndexAlias进行查询/README.md)
10. [综合排序：Function Score Query 优化算分](part-2/4.10-综合排序：Function Score Query 优化算分/README.md)
11. [Term & Phrase Suggester](part-2/4.11-Term&PhraseSuggester/README.md)
12. [自动补全与基于上下文的提示](part-2/4.12-自动补全与基于上下文的提示/README.md)
13. [配置跨集群搜索](part-2/4.13-跨集群搜索/README.md)
### 第 5 章：分布式特性及分布式搜索的机制
1. [集群分布式模型及选主与脑裂问题](part-2/5.1-集群分布式模型及选主与脑裂问题/README.md)
2. [分片与集群的故障转移](part-2/5.2-分片与集群的故障转移/README.md)
3. [文档分布式存储](part-2/5.3-文档分布式存储/README.md)
4. [分片及其生命周期](part-2/5.4-分片及其生命周期/README.md)
5. [剖析分布式查询及相关性算分](part-2/5.5-剖析分布式查询及相关性评分/README.md)
6. [排序及 Doc Values & Fielddata](part-2/5.6-排序及DocValues&Fielddata/README.md)
7. [分页与遍历：From, Size, Search After & Scroll API](part-2/5.7-分页与遍历-FromSize&SearchAfter&ScrollAPI/README.md)
8. [处理并发读写操作](part-2/5.8-处理并发读写/README.md)
### 第 6 章：深入聚合分析
1. [Bucket & Metric 聚合分析及嵌套聚合](part-2/6.1-Bucket&Metric聚合分析及嵌套聚合/README.md)
2. [Pipeline 聚合分析](part-2/6.2-Pipeline聚合分析/README.md)
3. [作用范围与排序](part-2/6.3-作用范围与排序/README.md)
4. [聚合分析的原理及精准度问题](part-2/6.4-聚合分析的原理及精准度问题/README.md)
### 第 7 章：数据建模
1. [对象及 Nested 对象](part-2/7.1-对象及Nested对象/README.md)
2. [文档的父子关系](part-2/7.2-文档的父子关系/README.md)
3. [Update By Query & Reindex API](part-2/7.3-UpdateByQuery&ReindexAPI/README.md)
4. [Ingest Pipeline & Painless Script](part-2/7.4-IngestPipeline&PainlessScript/README.md)
6. [Elasticsearch 数据建模实例](part-2/7.5-Elasticsearch数据建模实例/README.md)
7. [Elasticsearch 数据建模最佳实践](part-2/7.6-Elasticsearch数据建模最佳实践/README.md)
8. [第二部分总结回顾](part-2/7.7-第二部分总结与测验/README.md)
## 第三部分：管理 Elasticsearch 集群
### 第 8 章：保护你的数据
1. [集群身份认证与用户鉴权](part-3/8.1-集群身份认证与用户鉴权/README.md)
2. [集群内部安全通信](part-3/8.2-集群内部安全通信/README.md)
3. [集群与外部间的安全通信](part-3/8.3-集群与外部间的安全通信/README.md)
### 第 9 章：水平扩展 Elasticsearch 集群
1. [常见的集群部署方式](part-3/9.1-常见的集群部署方式/README.md)
2. [Hot & Warm 架构与 Shard Filtering](part-3/9.2-Hot&Warm架构与ShardFiltering/README.md)
3. [如何对集群进行容量规划](part-3/9.3-如何对集群进行容量规划/README.md)
4. [分片设计及管理](part-3/9.4-分片设计及管理/README.md)
5. [在私有云上管理 Elasticsearch 集群的一些方法](part-3/9.5-在私有云上管理与部署Elasticsearch集群/README.md)
6. [在公有云上管理与部署 Elasticsearch 集群](part-3/9.6-在公有云上管理与部署Elasticsearch集群/README.md)
### 第 10 章：生产环境中的集群运维
1. [生产环境常用配置与上线清单](part-3/10.1-生产环境常用配置与上线清单/README.md)
2. [监控 Elasticsearch 集群](part-3/10.2-监控Elasticsearch集群/README.md)
3. [诊断集群的潜在问题](part-3/10.3-诊断集群的潜在问题/README.md)
4. [解决集群 Yellow 与 Red 的问题](part-3/10.4-解决集群Yellow与Red的问题/README.md)
5. [提升集群写性能](part-3/10.5-提升集群写性能/README.md)
6. [提升进群读性能](part-3/10.7-集群压力测试/README.md)
7. [集群压力测试](part-3/10.7-集群压力测试/README.md)
8. 段合并优化及注意事项
9. [缓存及使用 Breaker 限制内存使用](part-3/10.9-缓存及使用Breaker限制内存使用/README.md)
10. [一些运维的相关建议](part-3/10.10-一些运维相关的建议/README.md)
### 第 11 章：索引生命周期管理
1. [使用 Shrink 与 Rollover API 有效管理时间序列索引](part-3/11.1-使用Shrink与RolloverAPI有效管理时间序列索引/README.md)
2. 索引全生命周期管理及工具介绍   
## 第四部分：利用 ELK 做大数据分析
### 第 12 章：用 Logstash 和 Beats 构建数据管道
1. Logstash 入门及架构介绍
2. Beats 介绍
### 第 13 章：用 Kibana 进行数据可视化分析
1. 使用 Index Pattern 配置数据
2. 使用 Kibana Discover 探索数据
3. 基本可视化组件介绍
4. 构建 Dashboard  
## 第 14 章：探索 X-Pack 套件
1. 用 Monitoring 和 Alerting 监控 Elasticsearch 集群
2. 用 APM 进行程序性能监控
3. 用机器学习实现时序数据的异常检测（上）
4. 用机器学习实现时序数据的异常检测(下）
5. 用 ELK 进行日志管理
6. 用 Canvas 做数据演示

## 第五部分：应用实战工作坊  
### 实战 1：电影搜索服务
1. 项目需求分析及架构设计
2. 将音乐数据导入 Elasticsearch
3. 搭建你的电影搜索服务
4. 基于 Java 和 Elasticsearch 构建应用
### 实战 2：Stackoverflow 用户调查问卷分析
1. 需求分析及架构设计
2. 数据 Extract & Enrichment
3. 构建 Insights Dashboard
### 备战：Elastic 认证
1. Elastic 认证介绍
2. 考点梳理
3. 集群的数据备份

# ELK 相关下载资源
1. ELK 7.x  推荐官网直接下载，如网速低，可使用以下链接 - 百度网盘下载（https://pan.baidu.com/s/1CRT3W4wEESglCBDnslk2AA）

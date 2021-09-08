# 监控 Elasticsearch 集群
监控
![](0.png)

## 课程demo
```
# Node Stats：节点级别的信息
GET _nodes/stats

#Cluster Stats: 集群级别的信息
GET _cluster/stats

#Index Stats: 索引级别的统计信息
GET kibana_sample_data_ecommerce/_stats

#Pending Cluster Tasks API: task信息,正在运行的task
GET _cluster/pending_tasks

# 查看所有的 tasks，也支持 cancel task
GET _tasks


GET _nodes/thread_pool
GET _nodes/stats/thread_pool
GET _cat/thread_pool?v
GET _nodes/hot_threads
GET _nodes/stats/thread_pool


# 设置 Index Slowlogs
# the first 1000 characters of the doc's source will be logged
PUT my_index/_settings
{
  "index.indexing.slowlog":{
    "threshold.index":{
      "warn":"10s",
      "info": "4s",
      "debug":"2s",
      "trace":"0s"
    },
    "level":"trace",
    "source":1000  
  }
}

# 设置查询
DELETE my_index
//"0" logs all queries
PUT my_index/
{
  "settings": {
    "index.search.slowlog.threshold": {
      "query.warn": "10s",
      "query.info": "3s",
      "query.debug": "2s",
      "query.trace": "0s",
      "fetch.warn": "1s",
      "fetch.info": "600ms",
      "fetch.debug": "400ms",
      "fetch.trace": "0s"
    }
  }
}

GET my_index


```
## 相关阅读

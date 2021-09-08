# 集群健康与问题排查
![](0.png)
![](1.png)
![](2.png)
![](3.png)
![](4.png)
## 课程demo
```
#案例1
DELETE mytest
PUT mytest
{
  "settings":{
    "number_of_shards":3,
    "number_of_replicas":0,
    "index.routing.allocation.require.box_type":"hott"
  }
}





# 检查集群状态，查看是否有节点丢失，有多少分片无法分配
GET /_cluster/health/

# 查看索引级别,找到红色的索引
GET /_cluster/health?level=indices


#查看索引的分片
GET _cluster/health?level=shards

# Explain 变红的原因,发现是box_type设置导致无法分配索引的分片
GET /_cluster/allocation/explain

GET /_cat/shards/mytest
GET _cat/nodeattrs


## demo2

DELETE mytest
GET /_cluster/health/

PUT mytest
{
  "settings":{
    "number_of_shards":3,
    "number_of_replicas":0,
    "index.routing.allocation.require.box_type":"hot"
  }
}

GET /_cluster/health/

#案例2, Explain 看 hot 上的 explain,发现变黄
因为本集群只有1个hotbode,而主分片和repica分片不该主分片在一个机器上
DELETE mytest
PUT mytest
{
  "settings":{
    "number_of_shards":2,
    "number_of_replicas":1,
    "index.routing.allocation.require.box_type":"hot"
  }
}

GET _cluster/health
GET _cat/shards/mytest
GET /_cluster/allocation/explain

PUT mytest/_settings
{
    "number_of_replicas": 0
}

```
## 相关阅读

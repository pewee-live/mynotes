# 缓存及使用Circuit Breaker限制内存使用
![](0.png)
## node cache: filter cache
![](1.png)

## shard request cache:缓存每个分片的查询结果,只缓存size=0的,将整个查询json作为key,所以查询时要保持json字段顺序
![](2.png)

## fielddata cache
![](3.png)

## 缓存失效
![](4.png)

## 内存分配
一半给JVM,一半给OS缓存索引

## 案例 segement占用缓存过高到时fullGC频繁
![](5.png)

## 案例2 fielddata 缓存太大导致频繁fullGC
![](6.png)

## 案例3 复杂聚合查询占用内存过大
![](7.png)

## 断路器
![](8.png)
![](9.png)

## 课程demo
```

## 查询节点使用情况
GET _cat/nodes?v

GET _nodes/stats/indices?pretty

GET _cat/nodes?v&h=name,queryCacheMemory,queryCacheEvictions,requestCacheMemory,requestCacheHitCount,request_cache.miss_count

GET _cat/nodes?h=name,port,segments.memory,segments.index_writer_memory,fielddata.memory_size,query_cache.memory_size,request_cache.memory_size&v


PUT /_cluster/settings
{
    "persistent" : {
       "indices.breaker.request.limit" : "90%"
    }
}

```
## 补充阅读
- https://www.elastic.co/blog/improving-node-resiliency-with-the-real-memory-circuit-breaker

# 一些运维相关的建议
![](0.png)
![](1.png)
![](2.png)
![](3.png)


## 备份
![](4.png)

## 定期更新
滚动升级
![](5.png)

## full restart 升级
![](6.png)



## 课程demo
```
# 一个索引的某个分片移动.移动一个分片从一个节点到另外一个节点
场景:某数据节点上有过多hot shard
POST _cluster/reroute
{
  "commands": [
    {
      "move": {
        "index": "index_name",
        "shard": 0,
        "from_node": "node_name_1",
        "to_node": "node_name_2"
      }
    }
  ]
}


# Fore the allocation of an unassinged shard with a reason

POST _cluster/reroute?explain
{
  "commands": [
    {
      "allocate": {
        "index": "index_name",
        "shard": 0,
        "node": "nodename"
      }
    }
  ]
}


# remove the nodes from cluster 
场景:想移除一个节点,或对一个机器维护有不希望集群变红,es会自动把此机器的节点删除
PUT _cluster/settings
{
  "transient": {
    "cluster.routing.allocation.exclude._ip":"the_IP_of_your_node"
  }
}

# Force a synced flush
场景需要重启一个节点
通过该命令,在一个节点上防止syncId,提高这些分片的recovery时间
POST _flush/synced

场景:控制allocation和recovery速率
# change the number of moving shards to balance the cluster
PUT /_cluster/settings
{
  "transient": {"cluster.routing.allocation.cluster_concurrent_rebalance":2}
}

# change the number of shards being recovered simultanceously per node
PUT _cluster/settings
{
  "transient": {"cluster.routing.allocation.node_concurrent_recoveries":5}
}

# Change the recovery speed
PUT /_cluster/settings
{
  "transient": {"indices.recovery.max_bytes_per_sec": "80mb"}
}

# Change the number of concurrent streams for a recovery on a single node
PUT _cluster/settings
{
  "transient": {"indices.recovery.concurrent_streams":6}
}


# Change the sinze of the search queue
场景:搜索响应时间过长,看到有reject指标增加,可适当提高
PUT _cluster/settings
{
  "transient": {
    "threadpool.search.queue_size":2000
  }
}

##清除缓存,
场景:节点上出现高内存占用,避免出现OOM,但影响查询性能
POST _cache/clear

# Clear the cache on a node
POST _cache/clear


#Adjust the circuit breakers
## 设置断路器,防止OOM
PUT _cluster/settings
{
  "persistent": {
    "indices.breaker.total.limit":"40%"
  }
}


```
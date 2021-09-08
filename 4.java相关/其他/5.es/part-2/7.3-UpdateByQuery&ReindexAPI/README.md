# Update by Query & Reindex
![场景](0.png)

## 课程demo
```
## update by query
* 在原有数据上,对数据做了重新索引的操作,适用于新增,字段
DELETE blogs/

# 1.写入文档
PUT blogs/_doc/1
{
  "content":"Hadoop is cool",
  "keyword":"hadoop"
}

# 2.查看 Mapping
GET blogs/_mapping

# 3.修改 Mapping，增加子字段，使用英文分词器
PUT blogs/_mapping
{
      "properties" : {
        "content" : {
          "type" : "text",
          "fields" : {
            "english" : {
              "type" : "text",
              "analyzer":"english"
            }
          }
        }
      }
    }


# 4.写入新文档
PUT blogs/_doc/2
{
  "content":"Elasticsearch rocks",
    "keyword":"elasticsearch"
}

# 5.查询新写入文档,发现更新的新字段在新加的文档中可以查询
POST blogs/_search
{
  "query": {
    "match": {
      "content.english": "Elasticsearch"
    }
  }

}

# 6.查询 Mapping 变更前写入的文档,发现没法查询到
POST blogs/_search
{
  "query": {
    "match": {
      "content.english": "Hadoop"
    }
  }
}


# 7.Update所有文档
POST blogs/_update_by_query
{

}

# 8.查询之前写入的文档,发现执行后能查到
POST blogs/_search
{
  "query": {
    "match": {
      "content.english": "Hadoop"
    }
  }
}

## es不允许修改mapping上原有字段,只能建新索引设置正确类型在导入
# 查询
GET blogs/_mapping
发现keyword是text类型,我去改发现报错不让改
PUT blogs/_mapping
{
        "properties" : {
        "content" : {
          "type" : "text",
          "fields" : {
            "english" : {
              "type" : "text",
              "analyzer" : "english"
            }
          }
        },
        "keyword" : {
          "type" : "keyword"
        }
      }
}



DELETE blogs_fix

# 创建新的索引并且设定新的Mapping
PUT blogs_fix/
{
  "mappings": {
        "properties" : {
        "content" : {
          "type" : "text",
          "fields" : {
            "english" : {
              "type" : "text",
              "analyzer" : "english"
            }
          }
        },
        "keyword" : {
          "type" : "keyword"
        }
      }    
  }
}

# Reindx API
POST  _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fix"
  }
}

GET  blogs_fix/_doc/1

# 测试 Term Aggregation,不开启fielddata的text无法做聚合,现在变成了keyword可以了
POST blogs_fix/_search
{
  "size": 0,
  "aggs": {
    "blog_keyword": {
      "terms": {
        "field": "keyword",
        "size": 10
      }
    }
  }
}


# Reindx API，version Type Internal
POST  _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fix",
    "version_type": "internal"
  }
}

# 文档版本号增加
GET  blogs_fix/_doc/1

# Reindx API，version Type Internal
POST  _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fix",
    "version_type": "external"
  }
}


# Reindx API，version Type Internal
POST  _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fix",
    "version_type": "external"
  },
  "conflicts": "proceed"
}

# Reindx 只创建不存在的文档
POST  _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fix",
    "op_type": "create"
  }
}

## 夸集群index
![cluster
](1.png)

![](2.png)

GET _tasks?detailed=true&actions=*reindex

```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/docs-reindex.html
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/docs-update-by-query.html

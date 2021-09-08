# 提升集群读性能
![](0.png)
![](1.png)
![](3.png)
![](4.png)

## 课程demo
```

##避免在查询时使用脚本,应该在index doc的时候通过ingres pipeline来处理好数据写入
PUT blogs/_doc/1
{
  "title":"elasticsearch"
}
GET blogs/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "title": "elasticsearch"
        }}
      ],
      
      "filter": {
        "script": {
          "script": {
            "source": "doc['title.keyword'].value.length()>5"
          }
        }
      }
    }
  }
}

使用filter优化,这样会使用cache
GET blogs/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {"title": "elasticsearch"}},
        {
          "range": {
            "publish_date": {
              "gte": 2017,
              "lte": 2019
            }
          }
        }
      ]
    }
  }
}
优化后
GET blogs/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {"title": "elasticsearch"}},
        { filter:{
          "range": {
            "publish_date": {
              "gte": 2017,
              "lte": 2019
            }
          }
		  }	
        }
      ]
    }
  }
}
```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/current/tune-for-search-speed.html

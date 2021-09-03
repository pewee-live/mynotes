# 使用 Search Template 和 Index Alias 查询

## Search Template
用来解耦程序和DSL ,因为es中查询语句的编排对查询结果算分和返回/查询性能都至关重要.开发工程师不需要去关心DSL模板.搜索工程师,性能工程师去做优化
具体见:
		https://www.elastic.co/guide/en/elasticsearch/reference/7.14/search-template.html

## 课程Demo

## search templates
```
为indxe 定义搜索模板,单字符串输入定义一个q来接收,模板名叫tmdb
POST _scripts/tmdb
{
  "script": {
    "lang": "mustache",
    "source": {
      "_source": [
        "title","overview"
      ],
      "size": 20,
      "query": {
        "multi_match": {
          "query": "{{q}}",
          "fields": ["title","overview"]
        }
      }
    }
  }
}
DELETE _scripts/tmdb

GET _scripts/tmdb

只需要指定模板名字,通过id指定,随时修改模板,比如权重等,很方便,对搜索者无感知
POST tmdb/_search/template
{
    "id":"tmdb",
    "params": {
        "q": "basketball with cartoon aliens"
    }
}

## 索引别名 可以实现零停机运维,比如改名或者reindex
PUT movies-2019/_doc/1
{
  "name":"the matrix",
  "rating":5
}

PUT movies-2019/_doc/2
{
  "name":"Speed",
  "rating":3
}

POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "movies-2019",
        "alias": "movies-latest"
      }
    }
  ]
}

POST movies-latest/_search
{
  "query": {
    "match_all": {}
  }
}
##可以在别名索引中添加filter来过滤一些数据
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "movies-2019",
        "alias": "movies-lastest-highrate",
        "filter": {
          "range": {
            "rating": {
              "gte": 4
            }
          }
        }
      }
    }
  ]
}

POST movies-lastest-highrate/_search
{
  "query": {
    "match_all": {}
  }
}



```

# 基于词项和基于全文的搜索
match query,matchphase和queryString会对输入分词在做查询
## 课程demo

```
DELETE products
PUT products
{
  "settings": {
    "number_of_shards": 1
  }
}


POST /products/_bulk
{ "index": { "_id": 1 }}
{ "productID" : "XHDK-A-1293-#fJ3","desc":"iPhone" }
{ "index": { "_id": 2 }}
{ "productID" : "KDKE-B-9947-#kL5","desc":"iPad" }
{ "index": { "_id": 3 }}
{ "productID" : "JODL-X-1937-#pV7","desc":"MBP" }

GET /products
#term查询,对输入不做处理
#用iPhone查询不到,因为es索引默认用standard分词吧他转成了小写字母,term查询会用你输入的原term做匹配,如果要用term查询,text有个keyword字段可以索引原文本.或者使用match查询
POST /products/_search
{
  "query": {
    "term": {
      "desc": {
        //"value": "iPhone"
        "value":"iphone"
      }
    }
  }
}

POST /products/_search
{
  "query": {
    "term": {
      "desc.keyword": {
        //"value": "iPhone"
        //"value":"iphone"
      }
    }
  }
}


POST /products/_search
{
  "query": {
    "term": {
      "productID": {
        "value": "XHDK-A-1293-#fJ3"
      }
    }
  }
}

POST /products/_search
{
  //"explain": true,
  "query": {
    "term": {
      "productID.keyword": {
        "value": "XHDK-A-1293-#fJ3"
      }
    }
  }
}

//当然也可以用match查询,match查询会对查询分词
//这里查不出来
POST /bsp-prd-2022.07.07/_search
{
  "query": {
    "term": {
      "content": {
        "value": "登录"
      }
    }
    
  }
}
//这里可以,因为对输入做了分词
POST /bsp-prd-2022.07.07/_search
{
  "query": {
    "match": {
      "content": "登和录"
    }
    
  }
}
//这个查不到,换成登录可以
POST /bsp-prd-2022.07.07/_search
{
  "query": {
    "match_phrase": {
      "content": "登和录"
    }
    
  }
}
//queryString可以查询
POST /bsp-prd-2022.07.07/_search
{
  "query": {
    "query_string": {
      "default_field": "content",
      "query": "登 OR 录"
    }
    
  }
}


##term查询返回结果会有算分过程,
term中用constant_score转为filter跳过算分,返回结果更快,filter还能利用缓存
POST /products/_search
{
  "explain": true,
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "productID.keyword": "XHDK-A-1293-#fJ3"
        }
      }

    }
  }
}


#设置 position_increment_gap
DELETE groups
PUT groups
{
  "mappings": {
    "properties": {
      "names":{
        "type": "text",
        "position_increment_gap": 0
      }
    }
  }
}

GET groups/_mapping
#全文查询match,match_phase,query_string会对输入的查询分词分完的term列表再去查询,最后结果合并
POST groups/_doc
{
  "names": [ "John Water", "Water Smith"]
}

POST groups/_search
{
  "query": {
    "match": {
      "names": {
        "query": "Water Water",
        "operator": "AND"
      }
    }
  }
}

POST groups/_search
{
  "query": {
    "match_phrase": {
      "names": {
        "query": "Water Water",
        "slop": 100
      }
    }
  }
}


POST groups/_search
{
  "query": {
    "match_phrase": {
      "names": "Water Smith"
    }
  }
}
```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/term-level-queries.html
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/full-text-queries.html

# 单字符串多字段查询：Multi Match
## 三种场景
1. 最佳字段Best Fields

  * 如上一期的title和content存在竞争和关联,主要评分来自最佳匹配

2. 多数字段Most Fields

  * 处理英文内容通常将主字段分词,加入同义词来匹配更多文档.原文本加入子字段来提供精确匹配,其他字段作为匹配文档提高篇相关度的信号. 匹配字段越多分越高
  
3. 混合字段
  
  * 某些实体,需要在多个字段中确定信息,单个字段只能作为整体的一部分.希望在任何接触的字段中找到尽可能多的词
## 课程demo
``

## 最佳字段Best Fields:一个field匹配尽可能多的关键词的doc
POST blogs/_search
{
    "query": {
        "dis_max": {
            "queries": [
                { "match": { "title": "Quick pets" }},
                { "match": { "body":  "Quick pets" }}
            ],
            "tie_breaker": 0.2
        }
    }
}

multi_match查询,默认type是最佳字段best_fields,可以不传
POST blogs/_search
{
  "query": {
    "multi_match": {
      "type": "best_fields",
      "query": "Quick pets",
      "fields": ["title","body"],
      "tie_breaker": 0.2,
      "minimum_should_match": "20%"
    }
  }
}



POST books/_search
{	
"query": {
    "multi_match": {
        "query":  "Quick brown fox",
        "fields": "*_title"
    	}
	}
}


POST books/_search
{
"query": {
    "multi_match": {
        "query":  "Quick brown fox",
        "fields": [ "*_title", "chapter_title^2" ]
    }
}
}


## 多数字段Most Fields查询:返回更多field匹配到某个关键词的doc
DELETE /titles
PUT /titles
{
    "settings": { "number_of_shards": 1 },
    "mappings": {
        "my_type": {
            "properties": {
                "title": {
                    "type":     "string",
                    "analyzer": "english",
                    "fields": {
                        "std":   {
                            "type":     "string",
                            "analyzer": "standard"
                        }
                    }
                }
            }
        }
    }
}

PUT /titles
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "english"
      }
    }
  }
}

POST titles/_bulk
{ "index": { "_id": 1 }}
{ "title": "My dog barks" }
{ "index": { "_id": 2 }}
{ "title": "I see a lot of barking dogs on the road " }

这里查询文档1分数比2高,因为"analyzer": "english"把原文分词并做了同义词
如果要查询更匹配,可以mapping定义子字段
GET titles/_search
{
  "query": {
    "match": {
      "title": "barking dogs"
    }
  }
}

DELETE /titles
PUT /titles
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "english",
        "fields": {"std": {"type": "text","analyzer": "standard"}}
      }
    }
  }
}

POST titles/_bulk
{ "index": { "_id": 1 }}
{ "title": "My dog barks" }
{ "index": { "_id": 2 }}
{ "title": "I see a lot of barking dogs on the road " }

定义子字段不对原文做提取,使用multi_match来查询最匹配原文,这里第二个文档就排分高了
GET /titles/_search
{
   "query": {
        "multi_match": {
            "query":  "barking dogs",
            "type":   "most_fields",
            "fields": [ "title", "title.std" ]
        }
    }
}

multi_match中字段增加权重,比如增加title字段的权重,则结果会反过来
GET /titles/_search
{
   "query": {
        "multi_match": {
            "query":  "barking dogs",
            "type":   "most_fields",
            "fields": [ "title^50", "title.std" ]
        }
    }
}


## 跨字段搜索:在多个字段中进行查询，就好像这些字段是一个字段
如某人的地址
{
	"street":"5 street",
	"city":"london",
	"country":"UK",
	"postcode":"AAA"
}
以下查询可以满足担忧如下问题

1.当想query的词全部出现在制定fields中的时候,无法使用operator=and,算分是出现在各个字段上不好算分
2. 用copyto占用额外空间 

//这样查不出来,因为使用and 要求query的词全部要出现在某一个字段
{
   "query": {
        "multi_match": {
            "query":  "5 street AAA",
            "operator" : "and",
            "type":   "most_fields",
            "fields": [ "street", "city" ,"country","postcode"]
        }
    }
}

使用cross_fields
好处支持使用operator
还能在搜索时为单个字段提升权重
{
   "query": {
        "multi_match": {
            "query":  "5 street AAA",
            "operator" : "and",
            "type":   "cross_fields",
            "fields": [ "street", "city" ,"country","postcode"]
        }
    }
}

```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/query-dsl-dis-max-query.html

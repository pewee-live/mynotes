# 单字符串多字段查询：Dis Max Query
## 课程demo
```

PUT /blogs/_doc/1
{
    "title": "Quick brown rabbits",
    "body":  "Brown rabbits are commonly seen."
}

PUT /blogs/_doc/2
{
    "title": "Keeping pets healthy",
    "body":  "My quick brown fox eats rabbits on a regular basis."
}

查询发现文档一算分高于文档二
因为bool的should查询是将其下所有的查询算分后相加,文档二因为title没有brown导致算分很低,这个结果不符合我们的期望
在本bool查询中2个macth查询因为每个算分结果叠加导致互相竞争
POST /blogs/_search
{
    "query": {
        "bool": {
            "should": [
                { "match": { "title": "Brown fox" }},
                { "match": { "body":  "Brown fox" }}
            ]
        }
    }
}

采用disjunction max query,采用字段上最匹配的评分作为评分返回,这时查询Brown fox会返回第二条文档分数高
但是在查询Quick pets时,有发现2条的结果分数一样,因为这时只看最高分,不考虑其他匹配条件的分数了
POST blogs/_search
{
    "query": {
        "dis_max": {
            "queries": [
                { "match": { "title": "Quick pets" }},
                { "match": { "body":  "Quick pets" }}
            ]
        }
    }
}

通过tie_breaker添加后算分结果会将最评分_score+tie_breaker*其他匹配语句
0表示没加,1表示每条都相等,和bool的should一样了
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


```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/query-dsl-dis-max-query.html

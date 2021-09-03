# 综合排序：Function Score Query 优化算分

## Function Score Query
对查询返回的结果进行一系列重新算分,有以下默认函数计算分值
1. weight:为每个文档设置一个简单不规范化的权重
2. field value factor:使用该数值修改score,如点赞数,热度等
3. random score:每个用户使用随机的算分结果,单个用户每次看到的顺序是一致的,每个用户看到的顺序不同
4. 衰减函数:以某个字段的值为标准,越近分越高
5. script score:自定义脚本空值逻辑

## 课程Demo
```
DELETE blogs
PUT /blogs/_doc/1
{
  "title":   "About popularity",
  "content": "In this post we will talk about...",
  "votes":   0
}

PUT /blogs/_doc/2
{
  "title":   "About popularity",
  "content": "In this post we will talk about...",
  "votes":   100
}

PUT /blogs/_doc/3
{
  "title":   "About popularity",
  "content": "In this post we will talk about...",
  "votes":   1000000
}

### 查询语句为popularity,将投票数多的排在前面,新分数=投票数*老分数
POST /blogs/_search
{
  "query": {
    "function_score": {
      "query": {
        "multi_match": {
          "query":    "popularity",
          "fields": [ "title", "content" ]
        }
      },
      "field_value_factor": {
        "field": "votes"
      }
    }
  }
}

### 上面的因为投票数导致算分差距特别大,可以通过modifier来对算分做平滑处理,这里意思是,新分数=log(1 + 投票数)*老分数
POST /blogs/_search
{
  "query": {
    "function_score": {
      "query": {
        "multi_match": {
          "query":    "popularity",
          "fields": [ "title", "content" ]
        }
      },
      "field_value_factor": {
        "field": "votes",
        "modifier": "log1p"
      }
    }
  }
}
具体还有些函数见下
![modifier函数](1.png)

添加factor对分数曲线做更好的控制,效果如下
![factor](2.png)

		POST /blogs/_search
		{
		  "query": {
		    "function_score": {
		      "query": {
		        "multi_match": {
		          "query":    "popularity",
		          "fields": [ "title", "content" ]
		        }
		      },
		      "field_value_factor": {
		        "field": "votes",
		        "modifier": "log1p" ,
		        "factor": 0.1
		      }
		    }
		  }
		}


## Boost Mode 和Max Boost
![Boost Mode](3.png)

* 默认使用multipy,我们也可定义为相加
* 可以限制最高分

POST /blogs/_search
{
  "query": {
    "function_score": {
      "query": {
        "multi_match": {
          "query":    "popularity",
          "fields": [ "title", "content" ]
        }
      },
      "field_value_factor": {
        "field": "votes",
        "modifier": "log1p" ,
        "factor": 0.1
      },
      "boost_mode": "sum",
      "max_boost": 3
    }
  }
}

## 通过seed值对返回重新计算,seed值只要一样,看到的顺序就是一样的.seed不一样`,结果顺序也不同
POST /blogs/_search
{
  "query": {
    "function_score": {
      "random_score": {
        "seed": 911119
      }
    }
  }
}
```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/query-dsl-function-score-query.html

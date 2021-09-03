# Elasticsearch聚合分析简介

##聚合分析是es提供的一个统计分析的功能,针对全套数据而不是单个文档
## 课程Demo
- 需要通过Kibana导入Sample Data的飞机航班数据。具体参考“2.2节-Kibana的安装与界面快速浏览”
``

## bucket聚合:相当于mysql的group,将特定条件的文档分组,如按价格区间分类,按男女分类,按省份分类
## metric:相当于mysql的count,大多数做数学计算输出一个(min,max,sum,avg)或多个值(stats,percentiles,percentile_ranks)
#按照目的地进行分桶统计
GET kibana_sample_data_flights/_search
{
	"size": 0,
	"aggs":{
		"flight_dest":{
			"terms":{
				"field":"DestCountry"
			}
		}
	}
}



#查看航班目的地的统计信息，增加平均，最高最低价格
GET kibana_sample_data_flights/_search
{
	"size": 0,
	"aggs":{
		"flight_dest":{
			"terms":{
				"field":"DestCountry"
			},
			"aggs":{
				"avg_price":{
					"avg":{
						"field":"AvgTicketPrice"
					}
				},
				"max_price":{
					"max":{
						"field":"AvgTicketPrice"
					}
				},
				"min_price":{
					"min":{
						"field":"AvgTicketPrice"
					}
				}
			}
		}
	}
}



#aggs嵌套:价格统计信息+天气信息
GET kibana_sample_data_flights/_search
{
	"size": 0,
	"aggs":{
		"flight_dest":{
			"terms":{
				"field":"DestCountry"
			},
			"aggs":{
				"stats_price":{
					"stats":{
						"field":"AvgTicketPrice"
					}
				},
				"wather":{
				  "terms": {
				    "field": "DestWeather",
				    "size": 5
				  }
				}

			}
		}
	}
}

```
## 相关阅读
- https://www.elastic.co/guide/en/elasticsearch/reference/7.1/search-aggregations.html

## canvas
1. 导入sampledata,点击kibana'的canvas,查看我们之前导入默认数据的canvas
2. 导入bulk api测试数据
3. 执行下面的aggs
4. 点击canvas,导入cafe-canvas.json文件



POST elasticoffee/_search
{
  "size": 0, 
  "aggs": {
    "by": {
      "terms": {
        "field": "beverage.keyword",
        "size": 10
      }
    }
  }
}
# Logstash 入门及架构介绍
## 概念
![Logstash](1.jpg)
![概念](2.jpg)
![架构](3.jpg)
![示例](4.jpg)

* input plugins

![input](5.jpg)

* output plugins

![output](6.jpg)


* codec plugin

![codec](7.jpg)

* filter plugin
![filter](8.jpg)

* queue:有多个input的时候多个input-> codec -> queue -> filter -> out 来保证消息不丢失

  * memory : 断电数据丢失
  *  persitent :Queue.type.persisted,可以替代kafka等消息缓冲队列使用

![queue](9.jpg)
* 多pipeline

![多pipeline](10.jpg)

## 课程demo
```


# 一个 Demo， demo 运行
sudo bin/logstash -f logstash-filter.conf

# demo数据
127.0.0.1 - - [11/Dec/2013:00:01:45 -0800] "GET /xampp/status.php HTTP/1.1" 200 3891 "http://cadenza/xampp/navi.php" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:25.0) Gecko/20100101 Firefox/25.0"


## codec demo
## 单行数据
sudo bin/logstash -e "input{stdin{codec=>line}}output{stdout{codec=> rubydebug}}"
sudo bin/logstash -e "input{stdin{codec=>json}}output{stdout{codec=> rubydebug}}"
sudo bin/logstash -e "input{stdin{codec=>line}}output{stdout{codec=> dots}}"

## 多行数据
![multiline](11.jpg)
![multiline demo](12.jpg)
sudo bin/logstash -f multiline-exception.conf

# 多行数据，异常
Exception in thread "main" java.lang.NullPointerException
        at com.example.myproject.Book.getTitle(Book.java:16)
        at com.example.myproject.Author.getBookTitles(Author.java:25)
        at com.example.myproject.Bootstrap.main(Bootstrap.java:14)

## input plugin
![input demo](13.jpg)
-
## filter plugin:对 logstash event做处理
![filter demo](14.jpg)
* mutate:我们第一次安装logstash导入movielens数据用的就是他对数据处理
![mutate](15.jpg)

# 一个实例
https://github.com/onebirdrocks/geektime-ELK/blob/master/part-1/2.4-Logstash%E5%AE%89%E8%A3%85%E4%B8%8E%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE/movielens/logstash.conf

```

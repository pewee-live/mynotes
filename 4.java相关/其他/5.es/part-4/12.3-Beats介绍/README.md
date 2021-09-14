# Beats介绍
搜集数据用的
- filebeats: 日志文件,随机的
- metricbeats: 指标,可聚合的数据
- packetbeats:网络数据
- winlogbeats:windows事件日志
- auditbeats:审计数据
- heartbeats:运行事件监控
- functionBeats:无需服务器的采集器



## 课程demo
```
## metric beats 监控服务器指标信息
![0](0.jpg)
![1](1.jpg)
![2](2.jpg)
![3](3.jpg)
![4](4.jpg)




# 查看 metricbeat 模块
# 设置 metricbeat 的mysql 模块
# 启动运行
#
./metricbeat modules list
./metricbeat modules enable mysql
./metricbeat setup --dashboards

编辑\modules.d下的配置文件
在启动metricmeat
./metricbeat

# 安装mysql
create database db_example
use db_example;
show tables;
select * from user

curl localhost:8080/demo/add -d name=Mike -d email=mike@xyz.com -d tags=Elasticsearch,IntelliJ
curl localhost:8080/demo/add -d name=Jack -d email=jack@xyz.com -d tags=Mysql,IntelliJ
curl localhost:8080/demo/add -d name=Bob -d email=bob@xyz.com -d tags=Mysql,IntelliJ

curl 'localhost:8080/demo/all'

同理,用metricbeats开启es,es-xpack的modules,并连接上es,可以在stackmonoring中监控es集群
 ./metricbeat.exe setup -e
./metricbeat -c metricbeat.yml -e

##packetbeat
![5](5.jpg)

# 配置 packetbeat
修改 packetbeat.yml文件
打开type : mysql
type : http,打开es的9200和kibana的5601
再 chown root packetbeat.yml

# 启动
修改 packetbeat，打开 http 5601 9200 和 mysql 3306监控

sudo chown root packetbeat.yml
sudo ./packetbeat setup --dashboards
sudo ./packetbeat


# 查看所有 Filebeat 模块
# 查看所有的modules
./filebeat modules list

#
./filebeat modules enable mysql

```

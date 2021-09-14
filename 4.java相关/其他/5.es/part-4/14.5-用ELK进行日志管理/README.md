# 用 ELK 来做日志管理
## filebeats手机日志
![简介](0.jpg)
![流程](1.jpg)
![流程](2.jpg)

## 课程demo

## kibana实战
点击kibana-logs-view_setup_instructions,里面展示了filebeats的modules
```
./filebeat modules list
./filebeat modules enable system
./filebeat modules enable elasticsearch


## 进 modules.d 编辑相应的文件，修改log路径

./filebeat setup –dashboards

./filebeat export template | more

./filebeat -e

进入kibana的dashborad和logs看到filebeats的system信息(注意windows没有syslog,所以会报错!!要禁止system module)

```
以前从来没玩过这游戏,看到有私服了,打一个和基友玩玩.
看到了网上有windows的,自己搭个linux的用来记录,我自己的是centOS

## mongo搭建


1. 下载wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-5.0.8.tgz
2. 解压:tar -xzvf mongodb-linux-x86_64-rhel70-5.0.8.tgz 
3. 切换目录rm -rf mongodb-org-server-5.0.8-1.el7.x86_64.rpm && mv mongodb-linux-x86_64-rhel70-5.0.8 /usr/local/mongodb4 
4. 建数据目录mkdir -p /var/lib/mongo && mkdir -p /var/log/mongodb
5. 启动 mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork
6. 查看日志:vi /var/log/mongodb/mongod.log
7. 环境变量 vi /etc/profile,添加export PATH=/usr/local/mongodb4/bin:$PATH
8. 打mongo能连上就OK了


## java环境
yum install java-latest-openjdk.x86_64

## 下载jar

wget https://github.com/Grasscutters/Grasscutter/releases/download/v1.1.0/grasscutter-1.1.0.jar

## 放入资源并启动
资源下载:https://github.com/Grasscutters/Grasscutter/wiki/Running#starting-the-server
放入文件结构:

![1.jpg](1.jpg)

在grasscutter.jar 所在目录中创建 resources 文件夹并将 BinOutput 和 ExcelBinOutput 放入其中,
java -jar grasscutter.jar启动,如果希望后台启动,请使用nohup 命令:
nohup java -jar grasscutter.jar > nohup.out 2>&1 &

注意:项目的配置见:emu.grasscutter.Config

![2.jpg](2.jpg)

其中大部分配置不需要修改,除非你有自定义的需求或者端口冲突,可以自建Config.json文件来覆盖默认设置(推荐)或者下载gradle重编译jar包.

目录结构现在应该为:
![4.jpg](4.jpg)

## 米忽悠域名解析
这一步是把你的客户端有关米忽悠的api转发或者解析到我们自己的host,我这里用的是host解析.
解析详情见:https://github.com/Grasscutters/Grasscutter/wiki/Running#traffic-route-map

127.0.0.1 api-os-takumi.mihoyo.com 
127.0.0.1 hk4e-api-os-static.mihoyo.com 
127.0.0.1 hk4e-sdk-os.mihoyo.com 
127.0.0.1 dispatchosglobal.yuanshen.com 
127.0.0.1 osusadispatch.yuanshen.com 
127.0.0.1 account.mihoyo.com 
127.0.0.1 log-upload-os.mihoyo.com 
127.0.0.1 dispatchcntest.yuanshen.com 
127.0.0.1 devlog-upload.mihoyo.com 
127.0.0.1 webstatic.mihoyo.com 
127.0.0.1 log-upload.mihoyo.com 
127.0.0.1 hk4e-sdk.mihoyo.com 
127.0.0.1 api-beta-sdk.mihoyo.com 
127.0.0.1 api-beta-sdk-os.mihoyo.com 
127.0.0.1 cnbeta01dispatch.yuanshen.com 
127.0.0.1 dispatchcnglobal.yuanshen.com 
127.0.0.1 cnbeta02dispatch.yuanshen.com 
127.0.0.1 sdk-os-static.mihoyo.com 
127.0.0.1 webstatic-sea.mihoyo.com 
127.0.0.1 hk4e-sdk-os-static.hoyoverse.com 
127.0.0.1 webstatic-sea.hoyoverse.com 
127.0.0.1 sdk-os-static.hoyoverse.com 
127.0.0.1 api-account-os.hoyoverse.com 
127.0.0.1 hk4e-sdk-os.hoyoverse.com 
127.0.0.1 uspider.yuanshen.com 

linux
vi /etc/hosts
![3.jpg](3.jpg)

windows:改host

手机开代理规则转发设置规则








# **oracle free服务器保活**

## oracle免费服务器回收策略

回收策略可见:

https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm#compute__idleinstances#compute__idleinstances



![回收策略](./pic/1.jpg)

对于E1类型(2个X86服务器):CPU在95%时间使用率少于20%且网络使用率少于20%;

对于A1(ARM服务器): 除以上条件,添加了且内存占用少于20%



## 解决方案

通过增加cpu或内存使用率(ARM)来解除回收策略;

采用项目: https://github.com/flow2000/lookbusy

安装lookbusy:

```shell
mkdir lookbusy
cd lookbusy/
wget https://github.com/flow2000/lookbusy/archive/refs/heads/master.zip      
unzip master.zip
cd lookbusy-master/
chmod a+x configure
./configure
make
make install
```

添加服务:

```
vi /usr/lib/systemd/system/lookbusy.service
```

​		添加以下内容:

```shell
[Unit]
Description=lookbusy service

[Service]
Type=simple
ExecStart=/usr/local/bin/lookbusy -c 20 -m 5120MB
Restart=always
RestartSec=10
KillSignal=SIGINT

[Install]
WantedBy=multi-user.target
```

​    
​	参数-c指cpu使用率，-m指内存使用率。可以根据自己的实例配置来适当配置。



启动服务:

```shell
chmod +x /usr/lib/systemd/system/lookbusy.service
systemctl daemon-reload
systemctl enable lookbusy
systemctl start  lookbusy.service
```

## 查看服务器状态

```shell
htop
```

可以看到CPU占用

![status](./pic/2.jpg)





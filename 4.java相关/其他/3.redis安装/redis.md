# Redis安装
## 下载,安装
	http://redis.io/download
	$ wget http://download.redis.io/releases/redis-5.0.8.tar.gz
	$ tar xzf redis-5.0.8.tar.gz
	$ cd redis-5.0.8
	$ make deps/lua hiredis linenoise
	$ make
	若果没有GCC需要先安装GCC:yum install gcc
	若果make时报如下错误：
	zmalloc.h:50:31: error: jemalloc/jemalloc.h: No such file or directory
	zmalloc.h:55:2: error: #error "Newer version of jemalloc required"
	make[1]: *** [adlist.o] Error 1
	make[1]: Leaving directory `/data0/src/redis-2.6.2/src'
	make: *** [all] Error 2
	原因是jemalloc重载了Linux下的ANSI C的malloc和free函数。解决办法：make时添加参数。
	
	make MALLOC=libc

## 配置单个节点
	$ cd /app/redis
	$ ln -s /usr/local/bin
	复制redis-trib.rb和redis.conf到新建的bin文件
	$ cp /app/redis/redis-5.0.8/redis.conf bin/redis.conf
	$ cp /app/redis/redis-5.0.8/src/redis-server /app/redis/bin/redis-server
	$ cp /app/redis/redis-5.0.8/src/redis-cli /app/redis/bin/redis-cli
	$ cp /app/redis/redis-5.0.8/src/redis-trib.rb /app/redis/bin/redis-trib.rb
	修改redis.conf的bind,port,daemonize,pidfile,requirepass,protected-mode,注意有些主机只有内网网卡,这里配内网网卡的ip,可用ifconfig查看
	$ /app/redis/bin/redis-server /app/redis/bin/redis.conf 

## 配置集群节点
1. 配置文件 
	
			$ cd /app/redis/bin
			$ mkdir 6380 && cp redis.conf ./6380/redis-6380.conf && cd ./6380
			修改redis-6380.conf
				port  7000                                        //端口7000,7002,7003        
				bind 本机ip                                       //改为其他节点机器可访问的ip 可以使用ifconfig查看一下
				daemonize    yes                               //redis后台运行
				appendonly  yes                           //aof日志开启  有需要就开启，它会每次写操作都记录一条日志
				pidfile  /var/run/redis_7000.pid          //pidfile文件对应7000,7001,7002
				cluster-enabled  yes                           //开启集群  
				cluster-config-file  nodes_7000.conf   //集群的配置  配置文件首次启动自动生成 7000,7001,7002
				cluster-node-timeout  15000                //请求超时  默认15秒，可自行修改
				notify-keyspace-events Ex   key通知
			$ mkdir 6381 && cp ./6380/redis-6380.conf ./6381/redis-6381.conf && cd ./6381
			$ mkdir 6382 && cp ./6380/redis-6380.conf ./6382/redis-6382.conf && cd ./6382
			$ mkdir 6383 && cp ./6380/redis-6380.conf ./6383/redis-6383.conf && cd ./6383
			$ mkdir 6384 && cp ./6380/redis-6380.conf ./6384/redis-6384.conf && cd ./6384
			$ mkdir 6385 && cp ./6380/redis-6380.conf ./6385/redis-6385.conf && cd ./6385
			$ cd /app/redis/bin/6380 && ../redis-server ./redis-6380.conf 
			$ cd /app/redis/bin/6381 && ../redis-server ./redis-6381.conf
			$ cd /app/redis/bin/6382 && ../redis-server ./redis-6382.conf 
			$ cd /app/redis/bin/6383 && ../redis-server ./redis-6383.conf 
			$ cd /app/redis/bin/6384 && ../redis-server ./redis-6384.conf 
			$ cd /app/redis/bin/6385 && ../redis-server ./redis-6385.conf 
	
2. 启用集群

		$ redis-trib.rb  create  --replicas  1  172.29.0.10:6380   172.29.0.10:6381   172.29.0.10:6382  172.29.0.10:6383  172.29.0.10:6384  172.29.0.10:6385 
		$ ./redis-cli --cluster create 172.29.0.10:6380 172.29.0.10:6381 172.29.0.10:6382 172.29.0.10:6383 172.29.0.10:6384 172.29.0.10:6385 --cluster-replicas 1 -a 密码
		最后在确认yes
		
		如果没有安装ruby环境
		http://www.ruby-lang.org/en/downloads/
		$ tar -xvzf ruby-2.2.3.tgz    
		$ cd ruby-2.2.3
		$ ./configure
		$ make
		$ sudo make install
		$ruby -v
		
		或安装 RVM,用 RVM 安装 Ruby 环境
		$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
		$ curl -sSL https://get.rvm.io | bash -s stable
		$ source /etc/profile.d/rvm.sh
		$ rvm list known
		$ rvm install 2.4.2
		$ rvm list
		$ rvm remove 1.9.2
		$ rvm 2.0.0 --default
		注意如果集群有内外网卡,需要将cluster-config-file文件中自动生成的内网地址改为外网ip
# **elastic安装**

1. https://www.elastic.co/cn/downloads/elasticsearch下载最新包
2. 解压
	> tar -zxvf elasticsearch-6.4.2.tar.gz
3. 创建elastic 用户组和用户 elasticSearch不允许使用root用户启动
		
		#创建组
		groupadd elasticGroup
		#创建用户
		useradd elasticUser
		#设置密码
		passwd elasticUser
		#将用户添加到组
		usermod -G elasticGroup elasticUser
		#给用户elasticUser添加elastic目录的权限
		chown -R elasticUser/home/elastic
4. 修改config/jvm.options

		-XX:-AssumeMP

5. 切换到root用户修改配置sysctl.conf

		vi /etc/sysctl.conf 
		vm.max_map_count=655360
		sysctl -p
6. 修改config/elasticsearch.yml

		network.host: 改为自己的ip
		cluster.initial_master_nodes: ["node-1"]
		http.cors.enabled: true
		http.cors.allow-origin: "*"

7. 启动

		./elasticsearch -d -E node.name=node0 -E cluster.name=peweeElk -E path.data=node0_data
		./elasticsearch -d -E node.name=node1 -E cluster.name=peweeElk -E path.data=node1_data
		./elasticsearch -d -E node.name=node2 -E cluster.name=peweeElk -E path.data=node2_data
		./elasticsearch -d -E node.name=node3 -E cluster.name=peweeElk -E path.data=node3_data

8. 查看状态

		ip:9200/_cluster/health
		ip:9200/_cat/nodes
		ip:9200/_cat/plugins	

9. 安装插件

		bin/elasticsearch-plugin list
		bin/elasticsearch-plugin install analysis-icu
		bin/elasticsearch-plugin list


10. 集群配置,Elasticsearch具有三个配置文件位于config目录中：
	* elasticsearch.yml 用于配置Elasticsearch
	* jvm.options 用于配置Elasticsearch JVM设置
	* log4j2.properties 用于配置Elasticsearch日志记录

具体内容见:https://www.elastic.co/guide/en/elasticsearch/reference/7.4/setup.html
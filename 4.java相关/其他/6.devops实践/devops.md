# 微服务和云原生实践
需求: 微服务 + SAAS + 容器云一键部署

可运维架构理念:

1. 日志监控,结构化日志
2. 功能测试
3. 性能测试
4. 配置管理(动态配置,多环境)
5. mertric监控
6. 健康检查
7. 调用链监控
8. 安全性(RBAC鉴权)
9. 高可用,主备机制
10. 可扩展,可升级,蓝绿部署

日志集中异常监控:Sentry

## elk日志

1. es启动
		
		生成私钥证书:
		bin/elasticsearch-certutil ca
		这里生成CA证书,要输入密码，我这写空密码
		节点认证证书公钥
		bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
		修改每个节点的elasticsearch.yml配置,将elastic-stack-ca.p12文件(只需要此文件)复制到每个节点上的Elasticsearch配置目录中的一个目录中。比如我是放到了每个节点的config/certs目录下。
		设置初始密码:
		bin/elasticsearch-setup-passwords interactive
			
    
	    配置文件:
	    cluster.name: data-center
	    cluster.initial_master_nodes: node-1
	    node.name: node-1
	    network.host: 0.0.0.0
	    xpack.security.enabled: true
	    #集群节点之间https传输(基础安全)
	    xpack.security.transport.ssl.enabled: true
	    xpack.security.transport.ssl.verification_mode: certificate
	    xpack.security.transport.ssl.keystore.path: certs/elastic-certificates.p12
	    xpack.security.transport.ssl.truststore.path: certs/elastic-certificates.p12
	    xpack.security.transport.ssl.client_authentication: required
	    #这里以下可选开启api接口https传输(进阶安全) ,具体密钥和配置参考#https://www.elastic.co/guide/en/elasticsearch/reference/7.15/security-basic-setup-https.html#encrypt-kibana-#elasticsearch
	    xpack.security.authc.api_key.enabled: true
	    xpack.security.http.ssl.enabled: true
	    xpack.security.http.ssl.keystore.path: elastic-certificates.p12
	    xpack.security.http.ssl.truststore.path: elastic-certificates.p12
	    xpack.security.http.ssl.client_authentication: none
	    xpack.http.ssl.verification_mode: none
	    启动:
	    C:\develop\env\elasticsearch-7.14.0\bin\elasticsearch.bat
	    nohup ./elasticsearch  > es.log 2>&1 &
	    firewall-cmd --get-active-zones
	    firewall-cmd --zone=public --list-ports
	    firewall-cmd --zone=public --add-port=9200/tcp --permanent
    firewall-cmd --zone=public --add-port=9300/tcp --permanent
	    firewall-cmd --reload
	
	常见错误:	

	1. bootstrap check failure [1] of [3]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
	
			ulimit -Hn
		ulimit -Sn
	2. bootstrap check failure [2] of [3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
	
			vi /etc/sysctl.conf
			增加配置vm.max_map_count=262144
			sysctl -p
3. bootstrap check failure [3] of [3]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
	4. 只监听ipv6 导致无法访问
	
			vi /etc/default/grub
			add ipv6.disable=1 at line 6,like:
			GRUB_CMDLINE_LINUX="ipv6.disable=1 ..."
			grub2-mkconfig -o /boot/grub2/grub.cfg
			reboot



2. kibana
		
		修改配置:
		server.host: "0.0.0.0"
		elasticsearch.username: "kibana"
		elasticsearch.password: "000000"
		i18n.locale: "zh-CN"
		C:\develop\env\kibana-7.14.0-windows-x86_64\bin\kibana.bat
		nohup ./kibana  > kb.log 2>&1 &
		firewall-cmd --zone=public --add-port=5601/tcp --permanent
		firewall-cmd --reload

若是es开启了https访问,修改一下elasticsearch.hosts: ["https://localhost:9200"]
elasticsearch.ssl.verificationMode: none

修改 kibana-encryption-keys:

./kibana-encryption-keys generate

将生成的key贴如kibana.yml

3. logstash
	
		java日志:2021-09-23 16:26:28 |INFO  |main |HuaWeiSendMailService.java:51 |com.opto.service.huawei_mail.HuaWeiSendMailService | sendHuaweiProductDetailMail |发送华为发货邮件-开始
		logstash文件配置1:
		input {
		  #stdin {
		  #}
		  beats {
		    port => 7110
		  }
		  
		}
		filter {
		 mutate {
		    remove_field => [ "ecs","tags","agent","@version","input","host","mac","path", "host","@timestamp"]
		    #convert => [ "[geoip][coordinates]", "float" ]
		   }
		 grok {
		        match => { "message" => '(?<datetime>\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}\.\d{3}) \|(?<loglevel>[A-Z\s]{4,5}) \|(?<thread>[A-Za-z0-9/-]{4,40}) \|(?<content>.*)' }
		    }
		 mutate {
		    strip => ["loglevel"]
		    remove_field => ["message"]
		  }
		 date {
		    match => [ "datetime", "YYYY-MM-dd HH:mm:ss.SSS" ]
		    locale => cn
		    }
		}
		
		output {
		   if [app] == "bsp" {
		       if [logtype] == "dev" {
		           elasticsearch {
		             hosts => "http://192.168.7.49:9200"
		             user => "elastic"
		             password => "000000"
		             index => "bsp-dev-%{+YYYY.MM.dd}"
		           }
		       }
		       if [logtype] == "sit" {
		           elasticsearch {
		             hosts => "http://192.168.7.49:9200"
		             user => "elastic"
		             password => "000000"
		             index => "bsp-sit-%{+YYYY.MM.dd}"
		           }
		       }
		       if [logtype] == "prd" {
		           elasticsearch {
		             hosts => "http://192.168.7.49:9200"
		             user => "elastic"
		             password => "000000"
		             index => "bsp-prd-%{+YYYY.MM.dd}"
		           }
		       }
		   }
		   
		   #stdout { codec => rubydebug }
		}
		配置2:
		input {
		  stdin {
		  }
		  #beats {
		  #  port => 7110
		  #}
		  
		}
		filter {
		 mutate {
		    remove_field => [ "ecs","tags","agent","@version","input","host","mac","path", "host","@timestamp"]
		    #convert => [ "[geoip][coordinates]", "float" ]
		   }
		 mutate {
		    split => { "message" => "|" }
			add_field => { "datetime" => "%{[message][0]}"}
		    add_field => { "loglevel" => "%{[message][1]}"}
			add_field => { "thread" => "%{[message][2]}"}
			add_field => { "class" => "%{[message][3]}"}
			add_field => { "fullclass" => "%{[message][4]}"}
			add_field => { "method" => "%{[message][5]}"}
			add_field => { "content" => "%{[message][6]}"}
		  }
		 mutate {
		    strip => ["datetime"]
			strip => ["loglevel"]
			strip => ["thread"]
			strip => ["class"]
			strip => ["fullclass"]
			strip => ["method"]
			strip => ["content"]
		    remove_field => ["message"]
		  }
		}
		
		output {
		   #elasticsearch {
		   #  hosts => "http://localhost:9200"
		   #  index => "bsp-%{+YYYY.MM.dd}"
		   #}
		   stdout { codec => rubydebug }
		}
	
		启动:nohup ./logstash  -f logstash4.conf > ls.log 2>&1 &
		firewall-cmd --zone=public --add-port=7110/tcp --permanent
		firewall-cmd --reload
具体pattern参考:

	1. https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/ecs-v1/grok-patterns
	2. https://blog.csdn.net/zhengzaifeidelushang/article/details/101271007
	3. https://grokdebug.herokuapp.com/

当es开启了ssl的时候,logstash 不能够直接使用 PKCS#12类型的证书！
我们需要 第三种命令，去 logstash-node-1.p12 的证书中提取 pem证书
openssl pkcs12 -in logstash-node-1.p12 -clcerts -nokeys -chain -out ca.pem
执行后会得到logstash使用的pem证书，名为 ca.pem

在logstash.conf里
ssl => true
cacert => "/app/elk/logstash-7.15.1/config/ca.pem"

在logstash.yml里配置https,账号,密码和cert路径


4. filebeats

./filebeat -e -c filebeat.yml

	- type: log
	
	  # Change to true to enable this input configuration.
	  enabled: true
	
	  # Paths that should be crawled and fetched. Glob based paths.
	  paths:
	    - C:\Users\pewee\logs\bsp\*.log
	    #- c:\programdata\elasticsearch\logs\*
	  # 如果值为ture，那么fields存储在输出文档的顶级位置
	  fields_under_root: true   
	  fields:
	    app: bsp
	    logtype: dev
	
	  # Exclude lines. A list of regular expressions to match. It drops the lines that are
	  # matching any regular expression from the list.
	  exclude_lines: ['^DBG']
	
	  # Include lines. A list of regular expressions to match. It exports the lines that are
	  # matching any regular expression from the list.
	  #include_lines: ['^ERR', '^WARN']
	
	  # Exclude files. A list of regular expressions to match. Filebeat drops the files that
	  # are matching any regular expression from the list. By default, no files are dropped.
	  #exclude_files: ['.gz$']
	
	  # Optional additional fields. These fields can be freely picked
	  # to add additional information to the crawled log files for filtering
	  #fields:
	  #  level: debug
	  #  review: 1
	
	  ### Multiline options
	
	  # Multiline can be used for log messages spanning multiple lines. This is common
	  # for Java Stack Traces or C-Line Continuation
	
	  # The regexp Pattern that has to be matched. The example pattern matches all lines starting with [
	  multiline.pattern: ^\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}\.\d{3}
	
	  # Defines if the pattern set under pattern should be negated or not. Default is false.
	  multiline.negate: true
	
	  # Match can be set to "after" or "before". It is used to define if lines should be append to a pattern
	  # that was (not) matched before or after or as long as a pattern is not matched based on negate.
	  # Note: After is the equivalent to previous and before is the equivalent to to next in Logstash
	  multiline.match: after
	output.logstash:
	  # The Logstash hosts
	  hosts: ["localhost:7110"]
	  index: pewee-user
	  # Optional SSL. By default is off.
	  # List of root certificates for HTTPS server verifications
	  #ssl.certificate_authorities: ["/etc/pki/root/ca.pem"]
	
	  # Certificate for SSL client authentication
	  #ssl.certificate: "/etc/pki/client/cert.pem"
	
	  # Client Certificate Key
	  #ssl.key: "/etc/pki/client/cert.key"
	  
		解决filebeats自动停止的方法:做成服务随系统启动
		vi /usr/lib/systemd/system/filebeat.service
		filebeat.service文件内容:
	
		[Unit]
		Description=Filebeat sends log files to Logstash or directly to Elasticsearch.
		Documentation=https://www.elastic.co/products/beats/filebeat
		Wants=network-online.target
		After=network-online.target
		 
		[Service]
		Type=simple
		Environment="LOG_OPTS=-e"
		Environment="CONFIG_OPTS=-c /usr/local/src/filebeat-7.10.1-linux-x86_64/filebeat.yml"
		#这个Env-PATH_OPTS可不要
		Environment="PATH_OPTS=-path.home /usr/local/src/filebeat-7.10.1-linux-x86_64/filebeat -path.config /usr/local/src/filebeat-7.10.1-linux-x86_64/filebeat -path.data /usr/local/src/filebeat-7.10.1-linux-x86_64/data -path.logs /usr/local/src/filebeat-7.10.1-linux-x86_64/logs"
		#PATH_OPTS可不要
		ExecStart=/usr/local/src/filebeat-7.10.1-linux-x86_64/filebeat $LOG_OPTS $CONFIG_OPTS $PATH_OPTS
		 
		Restart=always
		 
		[Install]
		WantedBy=multi-user.target




		chmod +x /usr/lib/systemd/system/filebeat.service
		 
		systemctl daemon-reload
		systemctl enable filebeat
		systemctl start filebeat
		journalctl

kibana-stack-manament-index-patern,新建一个index-patern

kibana-discover可以看到数据

5. metricbeats

   修改metricbeat.yml

   ​	output.elasticsearch:  hosts: ["<es_url>"]  username: "elastic"  password: "<password>" setup.kibana:  host: "<kibana_url>"

   ./metricbeat modules enable system

   modules.d/system.yml中修改更多配置

   kibana面板加载mertric仪表板

   ./metricbeat setup 

   nohup ./metricbeat -e   > mb.log 2>&1 &

   


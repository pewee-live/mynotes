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

		C:\develop\env\elasticsearch-7.14.0\bin\elasticsearch.bat

2. kibana

		C:\develop\env\kibana-7.14.0-windows-x86_64\bin\kibana.bat

3. logstash
	
		java日志:2021-09-23 16:26:28 |INFO  |main |HuaWeiSendMailService.java:51 |com.opto.service.huawei_mail.HuaWeiSendMailService | sendHuaweiProductDetailMail |发送华为发货邮件-开始
		logstash文件配置1:
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
		 grok {
		        match => { "message" => '(?<datetime>\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}) \|(?<loglevel>[A-Z\s]{4,5}) \|(?<thread>[A-Za-z0-9/-]{4,40}) \|(?<content>.*)' }
		    }
		 mutate {
		    strip => ["loglevel"]
		    remove_field => ["message"]
		  }
		 date {
		    match => [ "datetime", "YYYY-MM-dd HH:mm:ss" ]
		    locale => cn
		    }
		}
		
		output {
		   #elasticsearch {
		   #  hosts => "http://localhost:9200"
		   #  index => "bsp-%{+YYYY.MM.dd}"
		   #}
		   stdout { codec => rubydebug }
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
具体pattern参考:

	1. https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/ecs-v1/grok-patterns
	2. https://blog.csdn.net/zhengzaifeidelushang/article/details/101271007
	3. https://grokdebug.herokuapp.com/

4. filebeats

./filebeat -e -c filebeat.yml

	- type: log
	
	  # Change to true to enable this input configuration.
	  enabled: true
	
	  # Paths that should be crawled and fetched. Glob based paths.
	  paths:
	    - C:\Users\pewee\logs\bsp\*.log
	    #- c:\programdata\elasticsearch\logs\*
	
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
	  multiline.pattern: ^\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}
	
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

5. kibana-stack-manament-index-patern,新建一个index-patern
6. kibana-discover可以看到数据

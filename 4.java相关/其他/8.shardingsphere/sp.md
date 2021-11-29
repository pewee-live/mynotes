## ShardingSphere

官方网站:https://shardingsphere.apache.org/

## 示例文件
		#定义两个个人数据源
		spring.shardingsphere.datasource.names = ds-0,ds-1
		#配置数据源ds-0
		spring.shardingsphere.datasource.ds-0.type = com.alibaba.druid.pool.DruidDataSource
		spring.shardingsphere.datasource.ds-0.driverClassName = com.mysql.jdbc.Driver
		spring.shardingsphere.datasource.ds-0.url = jdbc:mysql://47.93.6.5:3306/ds-0?useUnicode=true&characterEncoding=utf8&tinyInt1isBit=false&useSSL=false&serverTimezone=GMT
		spring.shardingsphere.datasource.ds-0.username = root
		spring.shardingsphere.datasource.ds-0.password = xinzhifu521
		#配置数据源ds-1
		spring.shardingsphere.datasource.ds-1.type = com.alibaba.druid.pool.DruidDataSource
		spring.shardingsphere.datasource.ds-1.driverClassName = com.mysql.jdbc.Driver
		spring.shardingsphere.datasource.ds-1.url = jdbc:mysql://47.93.6.5:3306/ds-1?useUnicode=true&characterEncoding=utf8&tinyInt1isBit=false&useSSL=false&serverTimezone=GMT
		spring.shardingsphere.datasource.ds-1.username = root
		spring.shardingsphere.datasource.ds-1.password = xinzhifu521
		#配置分片表t_order
		#指定真实数据节点
		spring.shardingsphere.sharding.tables.t_order.actual-data-nodes = ds-$->{0..1}.t_order_$->{0..2}
		＃ ##分库策略
		#分库分片健
		spring.shardingsphere.sharding.tables.t_order.database-strategy.inline.sharding-column = order_id
		#分库分片算法
		spring.shardingsphere.sharding.tables.t_order.database-strategy.inline.algorithm-expression = ds-$->{order_id % 2}
		#分表策略
		#分表分片健
		spring.shardingsphere.sharding.tables.t_order.table-strategy.inline.sharding-column = order_id
		#分表算法
		spring.shardingsphere.sharding.tables.t_order.table-strategy.inline.algorithm-expression = t_order_$->{order_id % 3}
		#自增主键字段
		spring.shardingsphere.sharding.tables.t_order.key-generator.column = order_id
		#自增主键ID 生成方案
		spring.shardingsphere.sharding.tables.t_order.key-generator.type = SNOWFLAKE
		#工作机器唯一id
		spring.shardingsphere.sharding.tables.t_order.key-generator.props.worker.id = 0000
		#
		spring.shardingsphere.sharding.tables.t_order.key-generator.max.tolerate.time.difference.milliseconds = 5
		
		#配置分片表t_order_item
		spring.shardingsphere.sharding.tables.t_order_item.actual-data-nodes = ds-$->{0..1}.t_order_item_$->{0..2}
		spring.shardingsphere.sharding.tables.t_order_item.database-strategy.inline.sharding-column = order_id
		spring.shardingsphere.sharding.tables.t_order_item.database-strategy.inline.algorithm-expression = ds-$->{order_id % 2}
		spring.shardingsphere.sharding.tables.t_order_item.table-strategy.inline.sharding-column = order_id
		spring.shardingsphere.sharding.tables.t_order_item.table-strategy.inline.algorithm-expression = t_order_item_$->{order_id % 3}
		spring.shardingsphere.sharding.tables.t_order_item.key-generator.column = item_id
		spring.shardingsphere.sharding.tables.t_order_item.key-generator.type = SNOWFLAKE
		
		#绑定表关系
		spring.shardingsphere.sharding.binding-tables = t_order , t_order_item
		
		#默认数据源，未分片的默认执行库
		spring.shardingsphere.sharding.default-data-source-name = ds-0
		
		#配置广播表
		spring.shardingsphere.sharding.broadcast-tables = t_config
		
		#是否开启SQL解析日志
		spring.shardingsphere.props.sql.show = true
		
		mybatis-plus.mapper-locations = classpath:mapping/*.xml
		mybatis-plus.map-underscore-to-camel-case = true

## 分片策略

标准分片策略

使用场景：SQL 语句中有>，>=, <=，<，=，IN 和 BETWEEN AND 操作符，都可以应用此分片策略。

在使用标准分片策略时，精准分片算法是必须实现的算法，用于 SQL 含有 = 和 IN 的分片处理；范围分片算法是非必选的，用于处理含有 BETWEEN AND 的分片处理。

一旦我们没配置范围分片算法，而 SQL 中又用到 BETWEEN AND 或者 like等，那么 SQL 将按全库、表路由的方式逐一执行，查询性能会很差需要特别注意。

精准分表算法同样实现 PreciseShardingAlgorithm 接口，并重写 doSharding() 方法。

	### 分库策略
	# 分库分片健
	spring.shardingsphere.sharding.tables.t_order.database-strategy.standard.sharding-column=order_id
	# 分库分片算法
	spring.shardingsphere.sharding.tables.t_order.database-strategy.standard.precise-algorithm-class-name=com.xiaofu.sharding.algorithm.dbAlgorithm.MyDBPreciseShardingAlgorithm
	# 分表策略
	# 分表分片健
	spring.shardingsphere.sharding.tables.t_order.table-strategy.standard.sharding-column=order_id
	# 分表算法
	spring.shardingsphere.sharding.tables.t_order.table-strategy.standard.precise-algorithm-class-name=com.xiaofu.sharding.algorithm.tableAlgorithm.MyTablePreciseShardingAlgorithm


inline
直接通过表达式来分库分表.缺点:不支持范围查询!!,只支持一个key作为分片键

hint分片策略
直接在代码中指定从哪个库表查询

complex分片
可以对多行数据键做分片




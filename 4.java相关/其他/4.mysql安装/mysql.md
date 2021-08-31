# Mysql安装
## 准备工作
1. 删除已安装的版本
	1. rpm -qa | grep mysql
	2. rpm -e mysql或强力删除rpm -e --nodeps mysql
## 下载,安装
	https://dev.mysql.com/downloads/mysql/选择合适的包,
	在 Centos7 系统下使用 yum 命令安装 MySQL，需要注意的是 CentOS 7 版本中 MySQL数据库已从默认的程序列表中移除，所以在安装前我们需要先去官网下载 Yum 资源包
	$ wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
	$ rpm -ivh mysql80-community-release-el7-3.noarch.rpm
	$ yum update
	$ yum install mysql-server
	修改my.cnf忽略大小写
	$ vi /etc/my.cnf
	添加lower_case_table_names=1
	default-authentication-plugin=mysql_native_password
	初始化:
	$ mysqld --initialize
	权限设置：
	$ chown mysql:mysql -R /var/lib/mysql
	启动 MySQL：
	$ systemctl start mysqld
	查看 MySQL 运行状态：
	$ systemctl status mysqld
	mysql8会自动生成初始密码,查看自动生成的初始密码
	$  grep 'temporary password' /var/log/mysqld.log

## 验证 MySQL 安装
	使用 mysqladmin 工具来获取服务器状态：

	使用 mysqladmin 命令来检查服务器的版本, 在 linux 上该二进制文件位于 /usr/bin 目录，在 Windows 上该二进制文件位于C:\mysql\bin 。
	$ mysqladmin --version
	更改密码
	mysqladmin -u root -p password 新密码
	然后会要求输入旧密码
	或者
	$ mysql -u root -p
	mysql> use mysql;
	mysql> update mysql.user set password=PASSWORD('Pass@word') where User='root';
	解决连接权限问题(如不能从远程连接)
	mysql> show databases;
	mysql> update user set Host = '%' where User = 'root';
	mysql> flush privileges;
	解决印mysql8的密码加密方式由mysql_native_password变更caching_sha2_password导致的1251问题:
	1. 更新驱动
	2. 变更mysql密码加密方式为
		ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password'; #修改加密规则 
		ALTER USER 'root'@'%' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER; #更新一下用户的密码 
		
		'root'   为你自己定义的用户名
		'localhost' 指的是用户开放的IP，可以是'localhost'(仅本机访问，相当于127.0.0.1)，可以是具体的'*.*.*.*'(具体某一IP)，也可以是 '%' (所有IP均可访问)		
		'password' 是你想使用的用户密码


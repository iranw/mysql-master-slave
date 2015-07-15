# Mysql主从复制配置

###### 1、master主服务器设置复制账号
```
# REPLICATION SLAVE ON *.* TO '帐号'@'从服务器IP' IDENTIFIED BY '密码';
mysql> GRANT REPLICATION SLAVE,RELOAD,SUPER ON *.* TO slave@'%' IDENTIFIED BY '123456';
```

###### 2、配置master数据库 `my.cnf`
```
[vagrant@localhost ~]$ sudo vim /etc/my.cnf 

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# 开启mysql二进制日志
log-bin=mysql-bin
# 设置服务器ID
server-id=1
# 设置需要做主从复制的数据库名字 可以多个写多行
binlog-do-db=test
# 设置不需要备份的数据库
binlog-ignore-db=mysql,information_schema
# 设置保留二进制日志的天数 设置7天
expire-logs-days=7
# 设置主机读写权限
read-only=0

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

```

# Mysql主从复制配置

------
### master主服务器配置

###### 1、master主服务器设置复制账号
```
# REPLICATION SLAVE ON *.* TO '帐号'@'从服务器IP' IDENTIFIED BY '密码';
mysql> GRANT REPLICATION SLAVE ON *.* TO slave@'%' IDENTIFIED BY '123456';
```
`注`:线上配置已经要有更严格的权限控制(限制访问特定库，特定ip访问)

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
# 设置需要做主从复制的数据库名字 多个数据库可以设置多行 也可以设置到单行
binlog-do-db=test
# 设置不需要备份的数据库 可以写多行，也可以写到一行
binlog-ignore-db=mysql,information_schema
# 设置保留二进制日志的天数 设置7天
expire-logs-days=7
# 设置主机读写权限
read-only=0

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

###### 3、导出mysql

关闭所有数据库的所有表并申请一个全局的读锁，防止写数据。
```
mysql> FLUSH TABLES WITH READ LOCK;
```

新开一个终端，导出数据库
```
# mysqldump -u root -p database_name>/root/test.sql
```

查看mysql master状态并解锁
mysql> show master status\G
*************************** 1. row ***************************
            File: mysql-bin.000003
        Position: 565
    Binlog_Do_DB: test
Binlog_Ignore_DB: mysql,information_schema
1 row in set (0.00 sec)
mysql> unlock tables;
```
`注`:记录上面SHOW MASTER STATUS输出的`File`和`Position`，并在从服务器上用CHANGE MASTER TO配置主服务器即可。

------
### slave服务器配置

###### 3、配置slave服务器`my.cnf`





###### 配置slave服务器`my.cnf`
```
[vagrant@localhost ~]$ cat /etc/my.cnf 
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# 开启mysql二进制日志
log-bin=mysql-bin
#设置服务器ID
server-id=2
# 设置需要主从复制的库 多个写多行
replicate-do-db = test
# 设置不需要复制的某个库
replicate-ignore-db=mysql,information_schema
# 开启中继日志
relay_log=mysql-relay-bin
# slave将复制事件写进自己的二进制日志
log-slave-updates = 1
#设置保留二进制日志的天数设置7天
expire-logs-days=7
#在从库开启该选项，避免在从库上进行写操作，导致主从数据不一致（对super权限无效）
read-only=1


[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[vagrant@localhost ~]$
```
`注`:read-only = 1 这个选项对于root用户无效，root用户照样可以执行插入更新的操作。

###### 配置master信息

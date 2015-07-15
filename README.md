# Mysql主从复制配置


### Mysql主服务器(master)配置

###### 1、master主服务器设置复制账号
```
# REPLICATION SLAVE ON *.* TO '帐号'@'从服务器IP' IDENTIFIED BY '密码';
mysql> GRANT REPLICATION SLAVE ON *.* TO slave@'%' IDENTIFIED BY '123456';
```
`注`:线上配置一定要有更严格的权限控制(限制访问特定库，特定ip访问)

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

a、关闭所有数据库的所有表并申请一个全局的读锁，防止写数据。
```
mysql> FLUSH TABLES WITH READ LOCK;
```

b、新开一个终端，导出数据库
```
# mysqldump -u root -p database_name>/root/test.sql
```

c、查看mysql master状态并解锁
```
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
------
### Mysql从服务器(slave)配置

###### 1、将主数据库数据导入到从库

创建需要主从的数据库
```
mysql> CREATE DATABASE IF NOT EXISTS test DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

将主库dumpmysql的数据导入到新建的数据库里面
```
# mysql -h localhost -uroot -p密码 test</root/test.sql
```
or
```
mysql> use test;
mysql> source /home/vagrant/test.sql;
```

###### 2、配置slave服务器`my.cnf`
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
`注`:read-only = 1 在从库开启该选项，避免在从库上进行写操作，导致主从数据不一致(对super权限无效)

###### 3、配置链接
```
mysql> slave stop;
Query OK, 0 rows affected (0.00 sec)

mysql> change master to 
    -> MASTER_HOST='172.16.1.201',          // 主服务器的IP地址
    -> MASTER_USER='slave',                 // 同步数据库的用户
    -> MASTER_PASSWORD='123456',            // 同步数据库的密码
    -> MASTER_CONNECT_RETRY=60,             // 如果从服务器发现主服务器断掉，重新连接的时间差(秒)
    -> MASTER_LOG_FILE='mysql-bin.000003',  // 主服务器二进制日志的文件名(前面要求记住的 File 参数)
    -> MASTER_LOG_POS=565;                  // 日志文件的开始位置(前面要求记住的 Position 参数)
Query OK, 0 rows affected (0.00 sec)

mysql> slave start;
Query OK, 0 rows affected (0.00 sec)

mysql>
```

###### 4、重启从服务器slave
```
$ sudo /etc/init.d/mysqld restart
```

###### 5、查看从服务器状态
```
mysql> show slave status\G;
```
`注`:注意一定要有下面两项，没有的话查看错误日志(less /var/log/mysqld.log)
```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```



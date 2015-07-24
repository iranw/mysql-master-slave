# Centos服务器安装Mysql服务

### 1、Yum安装mysql服务
```
#sudo yum install mysql-server
#sudo yum install mysql-devel
#/etc/init.d/mysqld start           #启动mysql
```

### 2、初始化root密码
```
#/usr/bin/mysqladmin -u root password '123456'
```

### 3、初始化mysql
```
#/usr/bin/mysql_secure_installation    #重置密码与上一步操作重复，可忽略。其他选项建议全部为Y
```

### 4、操作mysql
```
#sudo /etc/init.d/mysqld {start|stop|status|restart|condrestart|try-restart|reload|force-reload}
#sudo service mysqld {start|stop|status|restart|condrestart|try-restart|reload|force-reload}
```

------
###### 常用命令


添加管理员账户
```
# GRANT ALL PRIVILEGES ON my_database.* TO 'my_user'@'localhost' IDENTIFIED BY 'my_password' WITH GRANT OPTION;
> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
> FLUSH PRIVILEGES;
```

添加应用用户(智能增删改查，不能对数据库管理操作)
```
> GRANT ALTER,SELECT,INSERT,UPDATE,DELETE,CREATE,INDEX ON *.* TO 'admin'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
> FLUSH PRIVILEGES;
```

创建数据库
```
mysql> CREATE DATABASE IF NOT EXISTS test DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

查看数据库变量设置
```
mysql> show variables;
mysql> show variables like "%log%";
```

查看binlog
```
mysql> show master logs;
```

查看特定binlog内容
```
mysql> mysql> show binlog events in "mysql-bin.000001";
mysql> show binlog events in "mysql-bin.000001" limit 1,3/* 查看特定行 */;
```

删除所有binlog
```
mysql> reset master;
```

开启mysql5.6 general_log日志
```
# 修改/etc/my.cnf 在mysqld里添加变量
[vagrant@localhost ~]$ cat /etc/my.cnf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

# Recommended in standard MySQL setup
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 

general_log=1
general_log_file=/var/lib/mysql/query.log

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[vagrant@localhost ~]$
```

------
`注`:强大的`mysql`备份工具`Xtrabackup`分享
`注`:mysql性能测试工具 `sysbench`

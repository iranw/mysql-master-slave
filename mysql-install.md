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


------
`注`:强大的`mysql`备份工具`Xtrabackup`分享

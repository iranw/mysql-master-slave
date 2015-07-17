# Centos服务器Yum安装Mysql5.6服务

### 1、更新源
到[http://dev.mysql.com/downloads/repo/yum/](http://dev.mysql.com/downloads/repo/yum/)查看合适的yum源并下载
```
# wget -c http://dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
```

更新源
```
sudo yum localinstall mysql-community-release-el6-5.noarch.rpm
```

### 2、查找并安装mysql
```
# yum search mysql | grep 'server'
[vagrant@localhost ~]$ yum info mysql-community-server.x86_64
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.bit.edu.cn
 * extras: mirrors.btte.net
 * updates: mirror.bit.edu.cn
Installed Packages
Name        : mysql-community-server
Arch        : x86_64
Version     : 5.6.25
Release     : 2.el6
Size        : 236 M
Repo        : installed
From repo   : mysql56-community
Summary     : A very fast and reliable SQL database server
URL         : http://www.mysql.com/
License     : ......
Description : ......
#sudo yum install mysql-community-server
```

启动mysql
```
#sudo /etc/init.d/mysql start
```

### 3、初始化mysql
```
/usr/bin/mysqladmin -u root password '123456'
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

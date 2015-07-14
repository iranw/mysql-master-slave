# Centos服务器安装Mysql服务

###### 1、Yum安装mysql服务
```
#sudo yum install mysql-server
#sudo yum install mysql-devel
#/etc/init.d/mysqld start           #启动mysql
```

###### 2、初始化root密码
```
#/usr/bin/mysqladmin -u root password '123456'
```

###### 3、操作mysql
```
#sudo /etc/init.d/mysqld {start|stop|status|restart|condrestart|try-restart|reload|force-reload}
#sudo service mysqld {start|stop|status|restart|condrestart|try-restart|reload|force-reload}
```
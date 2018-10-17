### 首先删除mysql:

###sudo apt-get remove mysql-*

然后清理残留的数据

###dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

它会跳出一个对话框，你选择yes就好了
然后安装mysql

###sudo apt-get install mysql-client mysql-server

安装的时候会提示要设置root密码，如果你没有在卸载的时候去清理残留数据是不会提示你去设置root密码的
检查mysql是不是在运行

###sudo service mysql status

一般安装完成之后都是会自动运行的。
如果没有运行你可以

###sudo service mysql start

运行它





#### 配置远程连接mysql

```
MySQL>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;
MySQL>FLUSH PRIVILEGES;
```



#### 远程连接mysql时

报错:

ERROR 2003 (HY000): Can’t connect to MySQL server on ‘127.0.0.1’ (111)

解决方法：

```
sudo vi /etc/mysql/my.cnf

注释掉下面这行：
#bind-address           = 127.0.0.1

sudo /etc/init.d/mysql restart
```
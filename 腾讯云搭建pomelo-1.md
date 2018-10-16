#### 参考资料

```
#nodejs
https://www.cnblogs.com/baby123/p/6955396.html
#mysql
http://blog.csdn.net/z13615480737/article/details/78906598

```

# 

### 一、nodejs

下载nodejs

```
wget https://npm.taobao.org/mirrors/node/v8.0.0/node-v8.0.0-linux-x64.tar.xz

```

解压

```
tar -xvf  node-v8.0.0-linux-x64.tar.xz

```

测试是否有node 和 npm

```
cd node-v8.0.0-linux-x64/bin && ls
./node -v

```

做个链接

```
ln -s /www/node-v8.0.0-linux-x64/bin/node /usr/local/bin/node
    
ln -s /www/node-v8.0.0-linux-x64/bin/npm /usr/local/bin/npm

```

查看环境变量

```
echo $PATH

```

### 二、mysql

下载mysql的repo源 这个安装的mysql5.7.20

```
cd /usr/local/src/
wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm 
rpm -ivh mysql57-community-release-el7-8.noarch.rpm 
yum -y install mysql-server 

```

------

**默认配置文件路径:**

> 配置文件：/etc/my.cnf 
> 日志文件：/var/log/var/log/mysqld.log 
> 服务启动脚本：/usr/lib/systemd/system/mysqld.service 
> socket文件：/var/run/mysqld/mysqld.pid 

配置 my.cnf vim /etc/my.cnf

```
[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
server_id = 1
expire_logs_days = 3

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid


```

启动mysql服务

```
service mysqld restart

```

重置密码

```
 grep "password" /var/log/mysqld.log
 mysql -u root -p
 [第一步获取的输入密码]
 alter user 'root'@'localhost' identified by 'Root!!2018'; 
 flush privileges;

```

> 修改MySQL的登录设置： 
> \#vi /etc/my.cnf
> 在[mysqld]的段中加上一句：skip-grant-tables 保存并且退出vi。
> 重新启动mysqld
> 重新启动mysqld
> \#/etc/init.d/mysqld restart ( service mysqld restart )
> use mysql 
> update user set password=password("12345") where user="root";
> mysql 5.7的数据库没有了password字段 用的是authentication_string字段
> mysql> update mysql.user set authentication_string=password('root') where user='root' ;
> flush privileges;
> 修改密码之后在改回来
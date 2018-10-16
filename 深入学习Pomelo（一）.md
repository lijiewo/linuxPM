# 深入学习Pomelo（一）—— 配置MySql数据库

#### 前言：

前面配置好了pomelo基于websocket的聊天室。接下来，我们准备为这个聊天室增加一个注册系统，首先，在系统中安装数据库。根据实际项目需求，数据库只需要稳定，访问频度和压力都不大，所以我们选MySql，而不用Mongodb。官方文档中可以看到，还有个比较完整的mmorpg游戏的例子lordofpomelo，里面采用了MySql数据库，我们可以把它的这些相关代码以及部分数据库结构学习过来。只拷贝符合我们需求的代码。

#### 1. 安装MySql数据库：

```
sudo apt install mysql-server
sudo apt install mysql-client
sudo apt install libmysqlclient-dev

```

安装mysql-server的时候，显示如下信息，要求输入root密码：

> While not mandatory, it is highly recommended that you set a password
> for the MySQL administrative "root" user.

If this field is left blank, the password will not be changed.

> 

New password for the MySQL "root" user:

需要重复输入一次，设置好了root密码。
检查端口状态：sudo netstat -tap | grep mysql
如果看到有mysql 的socket处于 listen 状态则表示安装成功。

进入数据库：

```
sudo mysql -u root -p

```

-u 表示选择登陆的用户名， -p 表示登陆的用户密码，上面命令输入之后会提示输入密码，此时输入密码就可以登录到mysql。
可以直接在MySql命令行用SQL命令创建库、表，执行查询等等。

#### 2.安装图形化工具MySql Workbench

用命令行固然简单，但是有个图形化工具能更直观。可以直接用apt安装：
sudo apt install mysql-workbench
安装好之后的样子：

![img](http://upload-images.jianshu.io/upload_images/4669459-fd2ff66301505476.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/818/format/webp)

MySql-Workbench.png

登录进去之后，创建一个schema：Dev，然后执行下面的Sql语句：

```
CREATE TABLE `User` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(50) CHARACTER SET utf8 NOT NULL,
  `password` varchar(50) DEFAULT '',
  `loginCount` smallint(6) unsigned DEFAULT '0',
  `lastLoginTime` bigint(20) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `name_UNIQUE` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
```

创建一个user表，用于记录用户登录信息。也可以直接在图形界面设计：

![img](http://upload-images.jianshu.io/upload_images/4669459-282cfb996e5a98bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

table_user.png

**注意：**图中的name和password的长度都是16，是早期的截图。后面考虑密码加密等问题，还是将这两个字段都改成50，VARCHAR在数据库中长度是可变的，这里定义的长度只是最大长度，实际占用空间并不会比定义成16更多。

再创建一个玩家表，用于记录玩家在游戏中的数据：

```
CREATE TABLE `Player` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `userId` bigint(20) unsigned NOT NULL DEFAULT '0',
  `nickname` varchar(50) CHARACTER SET utf8 NOT NULL DEFAULT '',
  `level` smallint(6) unsigned DEFAULT '1',
  `experience` bigint(20) unsigned DEFAULT '0',
  PRIMARY KEY (`id`),
  UNIQUE KEY `INDEX_GAME_NAME` (`nickname`),
  KEY `INDEX_PALYER_USER_ID` (`userId`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1

```

这样我们最基本的数据库和表就配置好了。有了图形化工具，以后要增删字段就很方便。比如这个Player表，明显少记录了一项：玩家在游戏中的游戏代币数量，以后用到再加吧。

#### 3. 配置连接参数：

由于数据库的连接参数game-server和web-server都需要用到，所以最好放到一个共享目录。在项目根目录建立一个shared目录，再在下面建立个config目录，在config下面新建一个文件mysql.js，配置连接数据库的参数：

```
{
    "development": {
      "host" : "127.0.0.1",
        "port" : "3306",
        "database" : "Dev",
        "user" : "dev",
        "password" : "abcd1234"
    },

    "production": {
      "host" : "127.0.0.1",
        "port" : "3306",
        "database" : "Dev",
        "user" : "dev",
        "password" : "abcd1234"
    }
}

```

数据库名是我们建立的Dev,　账号最好不要用root。自己用root进入MySql后新建立一个对Dev库有完整权限的dev账号即可。我直接在workbench下建立的，点菜单上的Server，选"Users and Privileges"，点"Add Account"……


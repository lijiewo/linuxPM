# 深入学习Pomelo（二）—— 修改web-server



#### 前言：

前面配置好了MySql数据库，建立了User和Player两个表，并设置好了数据库连接参数。登录注册都只需要用短连接http即可，所以把这部分放在web-server里。现在，我们参考lordofpomelo里面的一些相关配置和程序来完善我们的聊天室。

#### １. 配置dao：

考虑到大数据高并发的情况，数据库访问其实是个很复杂的技术，不是写几条Sql语句那么简单，需要建立连接池什么的。这个dao用于在底层操作和上层逻辑之间建立一个接口，我们对数据库访问的相关操作就放到这个模块里面。如果后面需要更换数据库什么的，可以直接重写这部分代码即可，而不需要在项目的所有逻辑代码里面到处去改sql语句。

将lordofpomelo中web-server/lib目录复制拷贝到我们的chatofpomelo的web-server目录下。因为我们的User表没设计'from'项，打开userDao，修改createUser，去掉相关部分，修改后的程序如下：

```
userDao.createUser = function (username, password, cb){
  var sql = 'insert into User (name,password,loginCount,lastLoginTime) values(?,?,?,?)';
  var loginTime = Date.now();
  var args = [username, password, 1, loginTime];
  mysql.insert(sql, args, function(err,res){
    if(err !== null){
      cb({code: err.number, msg: err.message}, null);
    } else {
      var userId = res.insertId;
      var user = {id: res.insertId, name: username, password: password, loginCount: 1, lastLoginTime:loginTime};
      cb(null, user);
    }
  });
};

```

然后打开dao/mysql/dao-pool.js，看到第一行：var _poolModule = require('generic-pool'); 意味着我们需要在工程的package.json中增加generic-pool。我原来测试lordofpomelo工程时候发现，generic-pool工程版本只能是1.x版本，高了会有语法错误。具体问题实在没精力仔细研究，现在安装模块的时候就限制一下它的版本。

```
npm install generic-pool@"<2.0"

```

> chatofpomelo@0.0.2 /home/ace/prog/chatofpomelo-websocket/game-server
> └── generic-pool@1.0.12

将"generic-pool": "1.0.12",这一句描述加入package.json里面的dependencies下面。

**后记：**
翻了一下官方论坛，发现一个大神发出来lordofpomelo版本，修补了generic-pool版本问题或者还修补了其他各种问题：[https://github.com/NextZeus/lordofpomelo](https://link.jianshu.com?t=https://github.com/NextZeus/lordofpomelo)，于是我果断Fork了，然后把相关部分代码Copy下来放到我们的工程中，前面提到的generic-pool安装的版本限制什么的也就不用了，package.json中相关项写做*"generic-pool": ""**即可。不过如果像我这样前面安装过老版本，用**"*"**是不会重新安装的，只能下node_modules目录下删除了generic-pool模块后重新执行npm install。或者用现在的版本号：generic-pool@3.1.7会更靠谱。

#### 2. 配置MD5模块：

lordofpomelo的注册验证系统，账号密码全部是明文的，正式应用里面肯定不能这样。我们加一个md5模块：
npm install md5

> chatofpomelo@0.0.2 /home/ace/prog/chatofpomelo-websocket/game-server
> └─┬ md5@2.2.1
> 　 ├── charenc@0.0.2
> 　 ├── crypt@0.0.2
> 　 └── is-buffer@1.1.5

同样的，将"md5":"2.2.1"加到package.json里面。

**To be continue....**


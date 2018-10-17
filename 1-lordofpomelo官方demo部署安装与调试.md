# 1-lordofpomelo官方demo部署安装与调试

部署与运行环境：腾讯云，Ubuntu16.04，docker

# 1 下载源码

参考官网安装手册：[https://github.com/NetEase/pomelo/wiki/LordOfPomelo-](https://link.jianshu.com?t=https://github.com/NetEase/pomelo/wiki/LordOfPomelo-)安装指南

# 2 安装依赖包

考虑到国内网络环境，我安装了淘宝cnpm加速，因此修改脚步如下

```
# cat npm-install.sh 
cd ./game-server && mkdir -p logs && cnpm install -d
echo '============   game-server npm installed ============'
cd ..
cd ./web-server && npm install -d
echo '============   web-server cnpm installed ============'
cd ..

```

```
# sh npm-install.sh   #执行脚步，安装node模块

```

> webkit-devtools-agent报错：
> [webkit-devtools-agent@0.1.2 › v8-profiler@[git://github.com/c4milo/v8-profiler.git#v4.0.0\]](https://link.jianshu.com?t=git://github.com/c4milo/v8-profiler.git#v4.0.0%5D) install v8-profiler from git [git://github.com/c4milo/v8-profiler.git#v4.0.0](https://link.jianshu.com?t=git://github.com/c4milo/v8-profiler.git#v4.0.0), may be very slow, please keep patience
> make: Entering directory '/code/vol/lordofpomelo/game-server/node_modules/_v8-profiler@4.0.0@v8-profiler/build'
> CXX(target) Release/obj.target/profiler/cpu_profiler.o
> In file included from ../cpu_profiler.cc:1:0:
> ../cpu_profiler.h:19:57: error: ‘Arguments’ does not name a type
> static Handle<Value> GetProfilesCount(const Arguments& args);
> 解决办法：
> 参考：[http://nodejs.netease.com/topic/55f3e60a42a25b9a147b7cbd](https://link.jianshu.com?t=http://nodejs.netease.com/topic/55f3e60a42a25b9a147b7cbd)
> 直接从package.json去掉该模块，重新安装

# 3 安装数据库

按照官网配置即可。我用dockers部署了mysql。当然我的其他app也是dockers部署，这样在内部服务访问时都是通过内网环境IP访问。只需要对外暴露服务端口。

# 4 修改配置文件

主要是修改对外服务IP，内部访问IP

```
# ls master.json masterha.json servers.json 
master.json  masterha.json  servers.json

```

# 5 启动游戏

> 报错：
> [2017-04-30 09:59:55.504] [ERROR] console - getSysInfo failed! Error: Command failed: /bin/sh -c iostat
> /bin/sh: 1: iostat: not found
> 解决办法：# apt-get install -y --force-yes  sysstat
> 安装需要用到的系统监控工具

# 6 注册用户

![img](http://upload-images.jianshu.io/upload_images/3958697-5bfe66770fabffdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/641/format/webp)

image.png

> 注册报错：
> TypeError: callback is not a function
> at Object._poolModule.createPool.create [as create] (/code/vol/lordofpomelo/web-server/lib/dao/mysql/dao-pool.js:23:7)
> at Pool._createResource (/code/vol/lordofpomelo/web-server/node_modules/generic-pool/lib/Pool.js:279:42)
> at Pool._dispense (/code/vol/lordofpomelo/web-server/node_modules/generic-pool/lib/Pool.js:210:12)
> at Pool.acquire (/code/vol/lordofpomelo/web-server/node_modules/generic-pool/lib/Pool.js:406:10)
> at Object.NND.query (/code/vol/lordofpomelo/web-server/lib/dao/mysql/mysql.js:25:8)
> at Object.userDao.getUserByName (/code/vol/lordofpomelo/web-server/lib/dao/userDao.js:12:9)
> at /code/vol/lordofpomelo/web-server/app.js:54:11
> at callbacks (/code/vol/lordofpomelo/web-server/node_modules/express/lib/router/index.js:272:11)
> at param (/code/vol/lordofpomelo/web-server/node_modules/express/lib/router/index.js:246:11)
> at pass (/code/vol/lordofpomelo/web-server/node_modules/express/lib/router/index.js:253:5)
> 解决办法：
> 参考：[http://nodejs.netease.com/topic/58a95bcb489e266d5be92744](https://link.jianshu.com?t=http://nodejs.netease.com/topic/58a95bcb489e266d5be92744)
> generic-pool最新版是用的es6了，使用的promise而不是回调。修改对应代码即可。
> 已经有人改好了，在github上，可以下下来用：[https://github.com/NextZeus/lordofpomelo](https://link.jianshu.com?t=https://github.com/NextZeus/lordofpomelo)
> 把gameserver和webserver两个地方的4个dao mysql文件都改掉

# 7 测试游戏

![img](https://upload-images.jianshu.io/upload_images/3958697-6a837c546b38da13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

image.png

# 8 测试数据库

使用phpmyadmin连接数据库，修改表项。懒得练级了，我直接把等级调到了90

![img](https://upload-images.jianshu.io/upload_images/3958697-9e85cfc009928d38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/810/format/webp)

image.png

然而，并没有*用，回头看代码再说吧。


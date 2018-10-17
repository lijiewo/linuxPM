#               2-windows开发环境搭建

# 1 安装node 6，python2.7，webstorm，unity

都是安装包，点下一步。略
注意在安装的时候选择添加path环境变量。

# 2 安装cnpm

使用淘宝镜像 [http://npm.taobao.org/](https://link.jianshu.com?t=http://npm.taobao.org/)。

```
npm config -g set cache "D:\Program Files\nodejs\node_cache"
npm config -g set prefix "D:\Program Files\nodejs\node_global"
可以通过 npm root -g：
查看全局的包的安装路径，这里顺便修改一下默认全局安装路径
C:\Users\admin>npm install -g cnpm --registry=https://registry.npm.taobao.org

```

我的电脑-》属性； 环境变量path添加：D:\Program Files\nodejs\node_global

![img](//upload-images.jianshu.io/upload_images/3958697-a2da4c2afe67f58e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/611/format/webp)

image.png

```
C:\Users\admin>cnpm -v
cnpm@4.5.0 (D:\Program Files\nodejs\node_global\node_modules\cnpm\parse_argv.js)
npm@3.10.10 (D:\Program Files\nodejs\node_global\node_modules\cnpm\node_modules\npm\lib\npm.js)
node@6.10.2 (D:\Program Files\nodejs\node.exe)
npminstall@2.29.2 (D:\Program Files\nodejs\node_global\node_modules\cnpm\node_modules\npminstall\lib\index.js)
prefix=D:\Program Files\nodejs\node_global
win32 x64 10.0.14393
registry=https://registry.npm.taobao.org

```

# 3 安装pomelo

官网：[https://github.com/NetEase/pomelo/wiki/%E5%AE%89%E8%A3%85pomelo](https://link.jianshu.com?t=https://github.com/NetEase/pomelo/wiki/%E5%AE%89%E8%A3%85pomelo)
注意，官网上说要c++的编译器，装完unity后基本就没有什么问题了。

```
C:\Users\admin>cnpm install pomelo -g
C:\Users\admin>pomelo -V
2.2.5

```

# 4 用webstorm调试项目

官网：[https://github.com/NetEase/pomelo/wiki/%E4%BD%BF%E7%94%A8-WebStorm-IDE-%E8%B0%83%E8%AF%95-Pomelo-%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F](https://link.jianshu.com?t=https://github.com/NetEase/pomelo/wiki/%E4%BD%BF%E7%94%A8-WebStorm-IDE-%E8%B0%83%E8%AF%95-Pomelo-%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)

这里我另建一个helloword项目试一下

## 4.1 初始化项目

```
D:\Program>pomelo init ./HelloWorld
选择1：1 for websocket(native socket)

D:\Program\HelloWorld>tree
卷 软件 的文件夹 PATH 列表
卷序列号为 00000095 0003:458C
D:.
├─game-server
│  ├─app
│  │  └─servers
│  │      └─connector
│  │          └─handler
│  ├─config
│  └─logs
├─shared
└─web-server
    ├─bin
    └─public
        ├─css
        ├─image
        └─js
            └─lib
                ├─build
                └─local
                    └─boot

```

## 4.2 配置webstorm启动参数

配置gameserver和webserver启动参数，如下：

![img](http://upload-images.jianshu.io/upload_images/3958697-86ed87b543658ac7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

安装项目依赖的npm包，可以先修改一下bat脚本，使用cnpm来安装，会比较快

![img](http://upload-images.jianshu.io/upload_images/3958697-6d363061e62148fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/582/format/webp)

image.png

## 4.3 启动server

点菜单run，然后分别启动webserver和gameserver

### webserver有warning：

> "D:\Program Files\JetBrains\WebStorm 2017.1.1\bin\runnerw.exe" "D:\Program Files\nodejs\node.exe" D:\Program\HelloWorld\web-server\app.js
> Warning: express.createServer() is deprecated, express
> applications no longer inherit from http.Server,
> please use:

> var express = require("express");
> var app = express();

> Web server has started.
> Please log on [http://127.0.0.1:3001/index.html](https://link.jianshu.com?t=http://127.0.0.1:3001/index.html)
> connect.multipart() will be removed in connect 3.0
> visit [https://github.com/senchalabs/connect/wiki/Connect-3.0](https://link.jianshu.com?t=https://github.com/senchalabs/connect/wiki/Connect-3.0) for alternatives
> connect.limit() will be removed in connect 3.0

按提示对应修改app.js代码即可

### gameserver有报错

> masterConsole encounters with error: Error: listen EADDRINUSE 3005

windows下可能是端口冲突了，修改master.json配置文件，换一个端口就好了

## 4.4 测试服务

网页打开：[http://127.0.0.1:3001/index.html](https://link.jianshu.com?t=http://127.0.0.1:3001/index.html)
测试连接OK

![img](http://upload-images.jianshu.io/upload_images/3958697-e11b87b234fd8825.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

image.png

## 4.5调试项目

从下图可以看到，增加打了一行日志，但是断点处却没有停下来，进入调试模式。
我的理解是gameserver的主进程是app.js，断点处是connector服务，不是同一个进程，因此webstorm无法跨进程去调试connector。

![img](http://upload-images.jianshu.io/upload_images/3958697-85df41ea27c18d3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

image.png

## 4.6remote调试

还好，从pomelo官网上学到一招，远程调试。

![img](https://upload-images.jianshu.io/upload_images/3958697-d7b46c719c3968c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

image.png

还有servers.json配置文件也要改一下：

```
{"id": "connector-server-1", "host": "127.0.0.1", "port": 3150, "clientHost": "127.0.0.1", "clientPort": 3010, "frontend": true, "args":" --debug=32011 "}

```

大功告成

![img](https://upload-images.jianshu.io/upload_images/3958697-2069e79801b6fc41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

image.png
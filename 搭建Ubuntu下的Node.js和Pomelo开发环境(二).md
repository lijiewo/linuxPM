# 搭建Ubuntu下的Node.js和Pomelo开发环境（二）—— 安装Pomelo和测试工程

#### 前言：

上一篇讲到用nvm安装Node.js系统，目前的最新版本是v7.10.0，最后的LTS版本是v6.10.3，理所当然选择v6.10.3。

由于nvm是安装在当前用户目录下的(我安装的路径是~/git/nvm)，用它安装的node js各个版本都在.../nvm/version/node目录下，也就是说是当前用户目录下。带来的好处是：后续用**npm install -g**命令安装的任何node插件都装在当前用户目录，包括接下来要我们要装的服务端引擎pomelo，以及pomelo会用到的各种依赖库，这些插件的安装、配置、运行等等，所有一切，都已不再需要用sudo了。

#### 1. 安装pomelo

关于[pomelo](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2FNetEase%2Fpomelo%2Fwiki%2FHome-in-Chinese)，抄一段官方的介绍吧：

> pomelo是一个游戏服务器框架，与以往单进程的游戏框架不同, 它是高性能、高可伸缩、分布式多进程的游戏服务器框架，并且使用很简单。它包括基础开发框架和一系列相关工具和库，可以帮助开发者省去游戏开发中枯燥的重复劳动和底层逻辑工作，免除开发者的重造轮子，让开发者可以更多地去关注游戏的具体逻辑，大大提高开发效率。pomelo强大的可伸缩性和灵活性使得pomelo也可以作为通用的分布式实时应用开发框架，用于一些高实时应用的开发，而且pomelo在很多方面的表现甚至超越了现有的开源实时应用框架。pomelo支持所有主流平台的客户端，并提供了客户端的开发库，使得客户端的开发变得很友好。

选择pomelo作为游戏服务端引擎的几大理由：

- 免费、开源、有相对比较活跃的社区和Q群支持，有比较丰富的文档和示例。
- 国内有不少公司在用它开发服务端，有不少成功的案例，并且不只是游戏领域。
- 源于网易的项目值得信任，官方一直在维护和更新。虽然有很长一段时间停滞于v1.2.3，但是最近又重启了，最新版本是v2.2.5。
- 公司现有Node.js服务端程序，学习成本很低。后续人才招聘也相对容易。

有了npm神器，pomelo的安装非常简单，只需要用npm全局安装一次：
`npm install pomelo -g`

**补充说明：** 貌似官方中文文档说明的“概述”部分没有内容，只能看英文文档。这里有个fork，应该是现在[新版本pomelo](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fcynron%2Fpomelo%2Fwiki%2F%25E4%25B8%25AD%25E6%2596%2587%25E6%2596%2587%25E6%25A1%25A3%25E9%25A6%2596%25E9%25A1%25B5)的维护者，看他的文档会更好一些。

#### 2.测试项目：

```
cd ~/prog
pomelo init ./test

```

显示如下：

> The default admin user is:
>
> username: admin
> password: admin
>
> You can configure admin users by editing adminUser.json later.
>
> Please select underly connector, 1 for websocket(native socket), 2 for socket.io, 3 for wss, 4 for >socket.io(wss), 5 for udp, 6 for mqtt: [1]

根据项目需要选择连接方式，大多数应用都选择1，用websocket建立连接。可以看出新的2.2.5比以前长期停滞的成熟版本1.2.3多了不少协议方式。嗯，我前面确实学习过1.2.3，刚学到一点皮毛，现在又需要学新东西了，活到老学到老，汗。废话少说，创建成功后：

```
cd ./test
sh npm-install.sh

```

给项目里面的game-server和web-server都安装需要的依赖项。具体配置看对应目录下的package.json文件内容。game-server服务端很快配置完了，客户端web-server在安装express的时候卡了很久，打开目录下的package.json看了一下，需要安装的express的版本号为3.4.8，貌似有点out了，ctrl-c中断下来，我先装个最新的express再说：

```
npm install express

```

全局安装最新的express，装完看到版本号是4.15.2。修改一下客户端package.json里面express的版本号，改成4.15.2。重新执行sh npm-install.sh，很快执行完，这样就配置好了。

测试一下。
进入game-server，运行:

```
pomelo start

```

然后ctrl-t新开一个终端窗口，进入到web-server目录下，运行：

```
node app

```

然后……报错了，妈蛋它不认识express.createServer()函数，汗，擅自升级客户端版本的后果。老老实实将express版本改回3.4.8，重新配置。耐心等待，老版本可能被npm仓库放到某个旮旯里面去了，找到它比较吃力，下载也吃力。

终于下完，重新运行node app，这次成功了，不过有个警告：

> Warning: express.createServer() is deprecated, express
> applications no longer inherit from http.Server,
> please use:
>
> var express = require("express");
> var app = express();
> ......
> Please log on [http://127.0.0.1:3001/index.html](https://link.jianshu.com?t=http%3A%2F%2F127.0.0.1%3A3001%2Findex.html)

早说嘛，早说我就不用换版本了，改一下源程序就行了。算了，反正只是test。打开链接，熟悉的柚子粗线了：

![img](http://upload-images.jianshu.io/upload_images/4669459-3680d761df125935.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/962/format/webp)

Screenshot from 2017-05-04 17-47-48.png

点一下“Test Game Server”的按钮，弹出“game server is ok”的消息框，表明一切顺利。好了，打完收工。

#### To be continue...（未完待续）
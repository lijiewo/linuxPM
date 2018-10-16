# 搭建Ubuntu下的Node.js和Pomelo开发环境（一）—— 安装Ubuntu和Node.js

#### 前言：

前文说到公司要新开一个视频直播相关的游戏，这个游戏的重点逻辑都在服务端，客户端相对简单。立项之前需要能看到一个简单的demo，然后，苦逼的我就从cocos客户端主程摇身一变，变身为基于node.js的服务端主程（临时工/备胎），把服务端框架搭建好以后，也许还会变身成各种需要的角色，反正就是那什么什么的一块砖，哪里需要往哪里搬。

新弄一台电脑过来做服务端开发机器，安装Linux系统。嗯，顺便说一句，Node.js服务端开发，菜鸟才在windows上扑腾，要装逼得从Linux开始。

#### 1.安装Ubuntu系统：

安装ubuntu 16.04 for x64，安装时选**英文**，这样目录名等均为英文，方便后续控制台输入目录名，同时减少中文系统带来的各种不可预见的问题。安装详细过程和设置略。

安装完毕后，在系统设置中加入中文支持。然后：

```
sudo apt update
sudo apt upgrade

```

升级系统各种东东到最新版本。

#### 2.安装中文输入法：

安装中文输入法是为了方便查询技术资料，当然QQ聊天，写写简书什么的也是需要的。

1. [http://pinyin.sogou.com/linux/](https://link.jianshu.com?t=http%3A%2F%2Fpinyin.sogou.com%2Flinux%2F) 下载搜狗拼音for Linux的64bit版本。
2. sudo apt install gdebi，下载安装一个安装器。
3. sudo gdebi sogoupinyin_2.1.0.0086_amd64.deb (其实可以双击下载好的deb文件安装了）
4. log out之后重新log in，输入法就可以用了。

由于是第三方软件，ubuntu不让直接安装。需要预先下载安装一个安装器gdebi。
参考：[http://jingyan.baidu.com/article/642c9d341b3ccb644a46f7ac.html](https://link.jianshu.com?t=http%3A%2F%2Fjingyan.baidu.com%2Farticle%2F642c9d341b3ccb644a46f7ac.html)

#### 3.安装ide编辑器：

抛开永不过时的神器vim、emacs，项目开发肯定得找一到两款顺手的ide。linux上好用的ide还是很多的：比如不老王者SublimeText，以前总用它撸撸啊，对，撸Lua；比如Atom，Github大神打造的前端开发利器，用来做nodejs开发想必是很好的；比如传说中的WebStorm，很多人说它是前端开发最强大的神器，没有之一；以及微软弃暗投明新近推出的Vs Code，这是孤陋寡闻的我见过的微软第一款跨平台产品。

SublimeText和WebStorm都是需要注册的。如果Linux下还用盗版，就真正亵渎了Linux的自由开源精神。我们还是需要有一点点理想，有一点点坚持。

果断选择Vs Code，前面做Cocos Creator开发的时候也一直用它，很顺手了。同样先到官网下载deb包。
下载页面：[https://code.visualstudio.com/Download](https://link.jianshu.com?t=https%3A%2F%2Fcode.visualstudio.com%2FDownload)
直接下载：[https://code.visualstudio.com/docs/?dv=linux64_deb](https://link.jianshu.com?t=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2F%3Fdv%3Dlinux64_deb)
同样用gdebi来安装，成功后dashboard里面找到它，运行后锁定到launcher。

文章没图很苍白啊，放一个Ubuntu下用Vs Code编辑器的效果图吧：

![img](//upload-images.jianshu.io/upload_images/4669459-ea27a9e7a2e9fe35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Screenshot from 2017-05-05 11-30-24.png

然后顺手把Atom也装了吧，即使开发nodejs不行用来做备胎也是极好的。如果要学React和React Native，这个也是官方推荐的ide：

```
sudo add-apt-repository ppa:webupd8team/atom  
sudo apt-get update 
sudo apt install atom

```

也放一张图吧：

![img](//upload-images.jianshu.io/upload_images/4669459-f6c8722001893179.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Atom.png

#### 4.安装Node.js：

先说说以前走过的弯路。这不是我第一次配置这个系统，现在算是轻车熟路，但是以前确实走过不少弯路，我觉得有必要记录一下。

**弯路1：**直接用apt install nodejs -legacy来安装，这样装下来的版本比较老，我前几个月测试的时候装的大概是v4.2.6 版本。而且这种安装之后，用起来各种不方便，极不推荐。

**弯路2：**在[node.js官网](https://link.jianshu.com?t=https%3A%2F%2Fnodejs.org%2Fen%2F)下载最新稳定版本，例如是v6.9.1，然后：

- 解压：
  `tar -xJf node-v6.9.1-linux-x64.tar.xz`
- 移动到opt目录下：
  `sudo mv node-v-6.9.1-linux-x64 /opt/node`
- 建立软链接：

```
sudo ln -s /opt/node/bin/node /usr/local/bin/node
sudo ln -s /opt/node/bin/npm /usr/local/bin/npm

```

- 配置环境：
  `sudo vim /etc/profile`
  在末尾添置三行:

```
export NODE_HOME=/opt/node
export PATH=$PATH:$NODE_HOME/bin 
export NODE_PATH=$NODE_HOME/lib/node_modules

```

然后：
`source /etc/profile`

- 设置npm使用淘宝源：
  在~/.bashrc中添加：

```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"

```

使修改立即生效：
`source ~/.bashrc`
这样就ok了，可以用node -v 和 npm -v测试一下。
**这样安装的缺点：**

- 如果需要在不同的Node.js版本之间找出最适合的开发版本，替换版本相当麻烦。
- 很多命令都必须用sudo来执行，运行的时候还有各种权限问题，诸多不便。

**推荐的方法：**

- 安装node js的版本管理工具nvm。首先安装git(如果尚未安装)：

```
sudo apt install git

```

- 用git下载：

```
git clone https://github.com/creationix/nvm.git

```

- 下载完毕后，配置终端启动时自动执行，在 ~/.bashrc, ~/.bash_profile, ~/.profile, 或者 ~/.zshrc 文件添加命令:
  `source ~/git/nvm/nvm.sh`
  我一般都在~/.bashrc中设置。
- 同时在这个文件中设置npm和nvm的源为淘宝镜像。文件最后部分是这样的：

```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
source ~/git/nvm/nvm.sh

```

- 最后，用nvm安装需要的node js版本，例如v6.9.1:
  `nvm install v6.9.1`
  安装最新稳定版本（一般就是官网上的Lastest Feature版本）：
  `nvm install stable`
  安装最新lts版本（Long-Term Support版本，建议大多数用户采用的版本）：
  `nvm install lts`
  可以同时安装多个版本，方便地用`nvm use [版本号]`来切换，切换后用`nvm alias default [版本号]`来设置它为默认版本（下次启动才不会被换回去）。
- nvm的详细用法请参考：[http://www.kancloud.cn/summer/nodejs-install/71975](https://link.jianshu.com?t=http%3A%2F%2Fwww.kancloud.cn%2Fsummer%2Fnodejs-install%2F71975)




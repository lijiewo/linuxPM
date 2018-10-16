# 搭建Ubuntu下的Node.js和Pomelo开发环境（三）

#### 前言：

上一篇我们成功地运行了一个最简单的测试工程，其实就是HelloWorld工程了。这一篇我们要装一个pomelo教程中提到的聊天室chatforpomelo工程进行深入学习，同时，不只用官方的web-server来测试，还要用我们的cocos creator客户端来进行测试。

#### 1.安装chatofpomelo：

详细内容参考[教程：Chat源码下载与安装](https://link.jianshu.com?t=https://github.com/cynron/pomelo/wiki/chat%E6%BA%90%E7%A0%81%E4%B8%8B%E8%BD%BD%E4%B8%8E%E5%AE%89%E8%A3%85)

```
cd ~/prog
git clone https://github.com/cynron/chatofpomelo-websocket.git

```

下面，我们不能完全跟着教程走，因为现在的版本新了很多。先修改一下服务端game-server里面的package.json，将里面的pomelo: "0.8.0"改为现在的版本2.2.5。然后顺手改一下app.js，打开发现已经改过了：app = express()；那为什么创建新工程的模板不修改一下呢？

然后回到chatofpomelo-websocket的根目录，运行：
`sh npm-install.sh`
配置成功后，按惯例下game-server目录运行pomelo start，下web-server目录运行node app

然后，打开浏览器，输入[http://127.0.0.1:3001/index.html](https://link.jianshu.com?t=http://127.0.0.1:3001/index.html)
输入与用户名和通道号就进入聊天室了。

测试发现，聊天室在用户退出后，会报错：

> [ERROR] pomelo-rpc - [mqtt-mailbox] rpc connector-server-1 callback timeout 30000, remote server 6 host: 127.0.0.1, port: 6050

后面还有几个错，都是从这个callback timeout衍生的问题。官方论坛查一下，貌似更新2.2.x后，很多人都碰到各种callback timeout的错，群里问了一下，说是以前程序写得不严密，callback为null没有问题，但是现在就会报错。

将pomelo版本回退到1.2.3是可以考虑的一个选择，但是这样，最好将node版本降到4.x版本，否则又会有其他不兼容的问题。

我们尝试修改一下服务端的connector/handler/entryHandler.js，将其中的onUserLeave函数修改为：

```
var onUserLeave = function(app, session) {
    if(!session || !session.uid) {
        return;
    }

    app.rpc.chat.chatRemote.kick(session, session.uid, app.get('serverId'), session.get('rid'), function(){
        console.log("====== kick callback over! ======");
    });
};

```

也就是在kick的最后加了一个回调函数。然后，修改chat/remote/chatRemote.js，将最后的kick函数修改为：

```
ChatRemote.prototype.kick = function(uid, sid, name,cb) {
    var channel = this.channelService.getChannel(name, false)
    // leave channel
    if( !! channel) {
        channel.leave(uid, sid);
    }
    var username = uid.split('*')[0];
    var param = {
        route: 'onLeave',
        user: username
    };
    channel.pushMessage(param);
    cb();
};

```

就是在函数入口增加一个cb的参数，函数最后加上cb()来回调。

最后开启聊天室测试，一切正常。其实我也不知道为什么这么改，靠摸索测试来的，反正就是**加上回掉，加上回调，加上回调**。重要的事情说三遍。

#### 2. 安装Cocos Creator客户端

参考：[http://www.jianshu.com/p/c64f7607adee](https://www.jianshu.com/p/c64f7607adee)
感谢作者提供的库文件以及其他所有的贡献。在windows机器上（Creator不支持linux， sigh！），进入作者的[github主页](https://link.jianshu.com?t=https://github.com/eddy2015/ccc-pomelo-chat-client)，下载源码放到CocosCreator工程目录下。

作者用的creator为1.1版本，在1.3版本以前这个库貌似都能正常运行，但是creator升级1.4以后，会报各种错啊，仔细看了一下，作者源码大概需要做一下修改：

1. pomelo-client中第5行，将var Protocol = window.Protocol修改为:

```
var Proctocol = require("protocol");

```

1. protobuf.js第37行：修改为：

```
})(typeof(window) == "undefined" ? module.exports :{}, this);

```

就是将{this.protobuf = {}}，修改为{}。原来的用法貌似有语法错误。

1. protocol.js最后，第350行，修改为：

```
})(typeof(window)=="undefined" ? module.exports : {}, typeof(window)=="undefined" ? Buffer : Uint8Array, this);

```

还是跟第二个修改一样的问题。

如此，pomelo客户端就完全正常了，**连警告都没有**。

Linux机器上将服务端启动起来，windows机器运行Creator客户端：

![img](https://upload-images.jianshu.io/upload_images/4669459-295f9ea75053f376.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/642/format/webp)

cccPomeloClient.png

点join加入房间，一切正常，可以和web的客户端正常聊天。
后续我们即将在这个聊天室基础上，不断学习和实践，构筑类似我们正式需求的服务端。


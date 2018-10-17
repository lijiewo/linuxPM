

#                 Ubuntu下Mongo的启动和关闭



### 后台启动命令

```
mongod --dbpath /data/platform/ --logpath /data/platform/dblog --master --oplogSize 64 --logappend --port 27027 --fork --maxConns=30000 --auth
```

执行以上命令如果出现

```
about to fork child process, waiting until server is ready for connections.
forked process: 20197
ERROR: child process failed, exited with error number 1
To see additional information in this output, start without the "--fork" option.
```

请确认日志目录是否存在，如目录不存在Mongo是不会自动创建此目录的

自行使用mkdir 命令创建目录



### gameserver有报错

> masterConsole encounters with error: Error: listen EADDRINUSE 3005

windows下可能是端口冲突了，修改master.json配置文件，换一个端口就好了
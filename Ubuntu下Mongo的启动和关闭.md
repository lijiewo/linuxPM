

#                 Ubuntu下Mongo的启动和关闭

















### 后台启动命令

```
mongod --dbpath /data/db_data --logpath /data/db_data/log --master --oplogSize 64 --logappend --port=28144 --fork --maxConns=30000
```








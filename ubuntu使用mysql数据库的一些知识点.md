# ubuntu使用mysql数据库的一些知识点

#### 一、导入csv数据到mysql

用wps Spreadsheets　（windows下可以用excel）将客户或策划做的xlsx文件转换为csv格式，但是里面可能会有逗号。凡是有逗号的字段，会用双引号将其中内容“包围”起来，所以在输入数据库的时候必须特别说明。

```
LOAD DATA LOCAL INFILE "filename.csv"
INTO TABLE tableName
CHARACTER SET utf8
FIELDS TERMINATED BY ","
OPTIONALLY ENCLOSED BY '"'
ESCAPED BY '"';

```

注意，那个LOCAL描述很重要，不写的话权限错误，然后去百度各种权限设置就掉进沟沟里面了。FIELDS TERMINATED BY ","的意思是字段以逗号分隔；OPTIONALLY ENCLOSED BY '"'和ESCAPED BY '"'的意思就是文本字段可能以双引号开始双引号结束。

数据库和表都应该提前设置为utf-8的，否则乱码。

**其他问题:**
当CSV最后一个字段为中文时，在数据库里面一看看不出什么问题，但是点开字段内容或者列表到命令行就会发现，字段内容最后多了一个  **\r**。

我重新导入了一次，采取的方法是把最后一个字段换为数字类型字段。
另外，可以将前面导入的命令后面加一句：

```
lines terminated by "\r\n"

```

表示这个文件行结尾是\r\n，应该同样能解决问题。
系统应该是默认每一行以\n结尾，所以多出了一个\r。
我没有实测。理论上就是这样。

#### 还有好多，有时间再来记录。掉了无数次坑总结出来的。


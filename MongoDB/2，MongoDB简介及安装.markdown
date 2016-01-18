# MongoDB简介及安装


---
## **概述** ##
MongoDB是一个基于分布式文件存储的数据库开源项目。由C++语言编写。旨在为WEB应用提供可护展的高性能数据存储解决方案。

MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似json的bjson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。 

Mongo主要解决的是海量数据的访问效率问题，根据官方的文档，当数据量达到50GB以上的时候，Mongo的数据库访问速度是MySQL的10倍以上。Mongo的并发读写效率不是特别出色，根据官方提供的性能测试表明，大约每秒可以处理0.5万－1.5次读写请求。

## **主要特点** ##

 - 面向集合存储：意思是数据被分组存储在数据集中， 被称为一个集合（Collenction)。每个集合在数据库中都有一个唯一的标识名，并且可以包含无限数目的文档。集合的概念类似关系型数据库（RDBMS）里的表（table），不同的是它不需要定义任何模式（schema)。 
 - 模式自由：意味着对于存储在MongoDB数据库中的文件，我们不需要知道它的任何结构定义。提了这么多次"无模式"或"模式自由"，它到是个什么概念呢？例如，下面两个记录可以存在于同一个集合里面：  
{"welcome" : "Beijing"} {"age" : 25} 
 - 文档型：存储在集合中的文档，被存储为键-值对的形式。键用于唯一标识一个文档，为字符串类型，而值则可以是各种复杂的文件类型
 - 高效的数据存储：支持二进制数据及大型对象
 - 支持复制和故障恢复：提供Master-Master、Master-Slave模式的数据复制及服务器之间的数据复制
 - 自动分片：以支持云级别的伸缩性，支持水平的数据库集群，可动态添加额外的服务器
## **适用场合** ##

- 网站数据：Mongo非常适合实时的插入，更新与查询，并具备网站实时数据存储所需的复制及高度伸缩性。

- 缓存：由于性能很高，Mongo也适合作为信息基础设施的缓存层。在系统重启之后，由Mongo搭建的持久化缓存层可以避免下层的数据源过载。

- 大尺寸，低价值的数据：使用传统的关系型数据库存储一些数据时可能会比较昂贵，在此之前，很多时候程序员往往会选择传统的文件进行存储。

- 高伸缩性的场景：Mongo非常适合由数十或数百台服务器组成的数据库。Mongo的路线图中已经包含对MapReduce引擎的内置支持。

- 用于对象及JSON数据的存储：Mongo的BSON数据格式非常适合文档化格式的存储及查询。
## **不适用的场合** ##
 - 要求高度事务性的系统
 - 传统的商业智能应用
 - 复杂的跨文档（表）级联查询
## **在Windows系统下安装MongoDB** ##

> <font color="green">**运行平台：**</font><br/> 从2.2版本开始,MongoDB不支持Windows XP。请使用“最近版本的Windows使用MongoDB的最近版本。
	

### **1. MongoDB的下载** ###
 &emsp;&emsp;下载最新产品发布版的MongoDB到官网下载页面<http://www.mongodb.org/downloads>
### **2. 安装下载文件** ###
&emsp;&emsp;在Windows资源管理器,找到下载MongoDB msi文件,通常“位于默认“下载”文件夹中
### **3. 手动创建一个MongoDB的Windows服务** ###

   - 为数据库创建目录和日志文件：

> mkdir c:\data\db
mkdir c:\data\log

- 创建配置文件，同时指定 日志文件路径和DBPATH在配置文件中的设置：

> echo logpath=c:\data\log\mongod.log> "C:\Program Files\MongoDB 2.6 Standard\mongod.cfg"
echo dbpath=c:\data\db>> "C:\Program Files\MongoDB 2.6 Standard\mongod.cfg"

- 创建MongoDB的服务

> sc.exe create MongoDB binPath= "\"C:\Program Files\MongoDB 2.6 Standard\bin\mongod.exe\" --service --config=\"C:\Program Files\MongoDB 2.6 Standard\mongod.cfg\"" DisplayName= "MongoDB 2.6 Standard" start= "auto"

如果创建成功，会显示以下日志消息：

> [SC] CreateService SUCCESS

- 开启 MongoDB 服务

> net start MongoDB

- 查看是否开启成功
在浏览器里面键入http://localhost:27017/
出现 <font color="grey">It looks like you are trying to access MongoDB over HTTP on the native driver port. </font>说明成功开启

- 停止MongoDB的服务

> net stop MongoDB

- 要删除的MongoDB服务，先停止服务，然后运行以下命令：

> sc.exe delete MongoDB

## **开启Web管理信息界面** 
 MongoDB自带一个微型的web管理信息界面，需要修改/mongod.conf中的一个配置项：
```shell
httpinterface=true
```
打开浏览器访问<http://127.0.0.1:28017>
这个页面可以看到
1. 当前Mongodb的所有连接
2. 各个数据库和Collection的访问统计，包括：Reads, Writes, Queries, GetMores ,Inserts, Updates, Removes
3. 写锁的状态
4. 以及日志文件的最后几百行
5. 所有的MongoDB命令
## **windows 下启动MongoDB的rest接口**
启动mongodb后，在web端口访问情况下，点击List all commands会出现如下错误
```shell
REST is not enabled.  use --rest to turn on.
check that port 28017 is secured for the network too.
```
需要修改/mongod.conf中的一个配置项：
```shell
rest = true
```

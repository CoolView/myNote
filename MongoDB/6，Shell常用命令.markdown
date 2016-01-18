# Shell常用命令

连接到一个 **<font color  = "blue">mongod</font>**
------------

&emsp;在命令行提示符下，输入 mongo 命令来启动 mongo 程序
```shell 
C:\Users\bin>mongo
MongoDB shell version: 3.0.2
connecting to: test
```

&emsp;默认情况下 mongo 程序会自动尝试去连接到本机localhost的 27017 端口。 若要连接到一个不同的服务器或者不同端口，你可以使用参数 --host 。
<font color= "blue">如：如果你想创建一个“myTest”的数据库，先运行`use myTest`命令，之后就做一些操作（如：db.createCollection('user')）,这样就可以创建一个名叫“myTest”的数据库。</font>

shell命令基本使用
-------

&emsp;mongo 程序启动时会默认选定 test 数据库。

#### 1. 打印出当前的数据库名：
```shell
> db
test
>
```

#### 2. 列出所有数据库:

```shell
> show dbs
   admin  0.078GB
   db     0.078GB
   local  0.078GB
   test   0.078GB
> 
```
#### 3. 切换到一个新的数据库 db:
```shell
> use db
switched to db db
>
```
####4. MongoDB自带一个JavaScript Shell
&emsp;&emsp;它是一个JavaScript解释器，还是一个MongoDB的客户端，可以通过JavaScript与启动的数据库实例进行交互(Shell中命令区分大小写)。在Shell中，每当写完一句完整的JS代码，Shell就会将其结果返回。
####5. 查看某个数据库中所有的集合：show collections

```shell
> show collections
person
system.indexes
>
```

&emsp;&emsp;如果该数据库中有已经存在的集合，并该集合中插入了文档，那么使用该命令查看集合时会发现多了一个system.indexes的集合，它负责存储索引，这是因为在插入一个文档时，如果没有一个叫做“_id”的key，那么会自动加入一个“_id”的key，系统默认会为该key建立唯一索引，所以在增加一个system.indexes的集合。
####6. 删除数据库中指定的集合：db.集合名.drop()。

```shell
> db.perso.drop()
true
>
```

####7. 删除当前数据库：db.dropDatabase()。
####8. shell中的help函数：
当进入到某个数据库中，要如何知道可以使用哪些操作呢？此时就可以使用help函数，如下图，就能够列出数据库级别有哪些用法了，当然除了数据库级别的help，还有集合级别的help，使用方法为：db.集合名.help()。在函数名称后面不添加“（）”还可以查看函数的源码。
```shell
> db.help()
DB methods:
        db.adminCommand(nameOrDocument) - switches to 'admin' db, and runs comma
nd [ just calls db.runCommand(...) ]
        db.auth(username, password)
        db.cloneDatabase(fromhost)
        db.commandHelp(name) returns the help for the command
        db.copyDatabase(fromdb, todb, fromhost)
        db.createCollection(name, { size : ..., capped : ..., max : ... } )
        db.createUser(userDocument)
        db.currentOp() displays currently executing operations in the db
        db.dropDatabase()
        db.eval(func, args) run code server-side
        db.fsyncLock() flush data to disk and lock server for backups
        db.fsyncUnlock() unlocks server following a db.fsyncLock()
        db.getCollection(cname) same as db['cname'] or db.cname
        db.getCollectionInfos()
        db.getCollectionNames()
        db.getLastError() - just returns the err msg string
        db.getLastErrorObj() - return full status object
        db.getLogComponents()
        db.getMongo() get the server connection object
        db.getMongo().setSlaveOk() allow queries on a replication slave server
        db.getName()
        db.getPrevError()
        db.getProfilingLevel() - deprecated
        db.getProfilingStatus() - returns if profiling is on and slow threshold
        db.getReplicationInfo()
        db.getSiblingDB(name) get the db at the same server as this one
        db.getWriteConcern() - returns the write concern used for any operations
 on this db, inherited from server object if set
        db.hostInfo() get details about the server's host
        db.isMaster() check replica primary status
        db.killOp(opid) kills the current operation in the db
        db.listCommands() lists all the db commands
        db.loadServerScripts() loads all the scripts in db.system.js
        db.logout()
        db.printCollectionStats()
        db.printReplicationInfo()
        db.printShardingStatus()
        db.printSlaveReplicationInfo()
        db.dropUser(username)
        db.repairDatabase()
        db.resetError()
        db.runCommand(cmdObj) run a database command.  if cmdObj is a string, tu
rns it into { cmdObj : 1 }
        db.serverStatus()
        db.setLogLevel(level,<component>)
        db.setProfilingLevel(level,<slowms>) 0=off 1=slow 2=all
        db.setWriteConcern( <write concern doc> ) - sets the write concern for w
rites to the db
        db.unsetWriteConcern( <write concern doc> ) - unsets the write concern f
or writes to the db
        db.setVerboseShell(flag) display extra information in shell output
        db.shutdownServer()
        db.stats()
        db.version() current version of the server
>
```

```shell
//使用函数
> db.getName()
test
>
```

```shell
//查看函数源代码
> db.getName
function (){
    return this._name;
}
>
```
####8. Shell内置的JS引擎可以直接执行执行JS代码
```shell
> function invokeEval(){
... return db.eval("return 123")
... }
> invokeEval()
123
>
```

####9. connect()
&emsp;&emsp;虽然Shell中提供的全局变量db指向当前连接的数据库，但还可以用其它的变量来保存其它连接的数据库，利用Shell中提供的connect()命令即可，
```shell
> db
test
> var udb = connect("127.0.0.1:27017/admin")
connecting to: 127.0.0.1:27017/admin
> udb
admin
> db
test
>
```

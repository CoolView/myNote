# shell中执行删除

###**删除**
1、删除集合中所有文档：`db.集合名.remove({})`，集合本身和索引不会被删除。
2、根据条件删除：db.集合名.remove({...})。
```shell
> db.person.find()
{ "_id" : 1002, "name" : "wang", "age" : 30 }
{ "_id" : 1001, "name" : "zhang", "age" : 20 }
{ "_id" : 1003, "name" : "zhang", "age" : 23 }
>
> db.person.remove({"age":20})
WriteResult({ "nRemoved" : 1 })
> db.person.find()
{ "_id" : 1002, "name" : "wang", "age" : 30 }
{ "_id" : 1003, "name" : "zhang", "age" : 23 }
>
```
使用`drop()`删除集合
如果你想删除整个"userdetails"集合，包含所有文档数据，可以执行以下数据：
```shell
>db.userdetails.drop()
```
使用`dropDatabase()`函数删除数据库
如果你想删除整个数据库的数据，你可以执行以下命令：
```shell
>db.dropDatabase()
```
执行命令前查看当前使用的数据库是一个良好的习惯，这样可以确保你要删除数据库是正确的，以免造成误操作而产生数据丢失的后果：
```shell
>db
test
>
```









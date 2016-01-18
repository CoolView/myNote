# MongoDB 备份

标签（空格分隔）： MongoDB

---

1、导出
导出可以使用mongoexport命令，导出会中断其它操作。常用参数：
-d：指明要导出的数据库
-c：指明要导出的集合
-o：指明要导出的文件名
--host：指明数据库主机地址（不写默认就是本机）
--port：指明数据库端口（不写默认就是27017）
如下图：导出test数据库中的person集合
```
C:\Users\bin>mongoexport -d test -c person -o C:\data\person.json
2015-06-02T14:43:02.493+0800    connected to: localhost
2015-06-02T14:43:02.495+0800    exported 6 records
```
2、导入
导入可以使用mongoimport命令，导入也会中断其它操作。常用参数：
--db：指明要导入到的数据库（如果数据库不存在会自动创建）
--collection：指明要导入到的集合（如果集合不存在会自动创建）
--file：数据文件地址
--host：指明数据库主机地址（不写默认就是本机）
--port：指明数据库端口（不写默认就是27017）
如下图：导入数据到test数据库中的person集合
```
C:\Users\bin>mongoimport --db test --collection person2 --file C:\data\person.json
2015-06-02T14:46:27.687+0800    connected to: localhost
2015-06-02T14:46:27.691+0800    imported 6 documents
```
3、运行时备份
运行时备份可以使用mongodump命令，它不会中断其它操作，但可能会遗漏数据，因为可能内存中的数据尚未写回数据库中。如下图：备份test数据库
```
C:\Users\bin>mongodump --host 127.0.0.1:27017 -d test -o C:\data\test
```
4、运行时恢复
运行时恢复可以使用mongorestore命令，如下图：恢复test数据库
```
C:\Users\bin>mongorestore --host 127.0.0.1:27017 -d test -directoryperdb C:\data\test\test
```
5、数据修复
当停电等不可逆转情况发生，由于MongoDB的存储结构导致会产生垃圾数据，这时候就可以使用数据库的自我修复，命令为`db.repairDatabase()`。





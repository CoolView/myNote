# MongoDB索引


---

##**一、索引的基本使用**
###**1、建立索引**
数据库会按照索引对数据进行一个排序，存储在一个地方，查询时先到这个地方进行定位，然后再去取真实数据。而MongoDB对不会采用任何索引的查询都会进行“全表扫描”，即查询整个集合。
在shell中为某个key建立索引的方法为调用集合的`ensureIndex`函数来构建索引，即索引是建立在集合之上的：`db.集合名.ensureIndex({key:1})`，其中的key表示为哪个key建立索引，1（大于0）表示升序建立索引数据，而-1（小于0）表示降序建立索引数据，如下图：为age这个键升序建立索引。
```shell
> db.person.ensureIndex({age:1})
```
还可以通过点表示法为内嵌文档建立索引，
```shell
> db.person.ensureIndex({"score.math":1})
```
###**2、在shell中查看数据库已建立的索引**
索引的描述信息存储在集合`system.indexes`中，这是系统提供的保留集合（创建数据库时），不能对其进行插入或删除操作。操作这个集合只能通过`ensureIndex`插入索引，`dropIndex`删除索引两个函数。
在`system.indexes`和`system.namespaces`集合中都能看到数据库已建立的索引，如下图：之前有说过，当插入一条文档到集合中时，如果该文档没有“_id”键时，系统会默认为文档加上该键，而从下图结果中可以发现，系统还会默认为“_id”键建立唯一索引。其中键“ns”是“数据库名.集合名”
```shell
> db.system.indexes.find()
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "test.system.users" }
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "test.person" }
{ "v" : 1, "key" : { "age" : 1 }, "name" : "age_1", "ns" : "test.person" }
>
> db.system.namespaces.find()
{ "name" : "test.system.indexes" }
{ "name" : "test.system.users" }
{ "name" : "test.system.users.$_id_" }
{ "name" : "test.person" }
{ "name" : "test.person.$_id_" }
{ "name" : "test.person.$age_1" }
```
###**3、索引名称**
集合中每个索引都会有一个字符串名字，来唯一标识这个索引，且MongoDB通过这个名称来操作索引。默认索引的名称规则为：keyname1_dir1_keyname2_dir2.....，keyname是构建索引的键名称，dir是代表其方向的数字。如创建索引{"a":1,"b":1}，其的默认名称是"a_1_b_1"。也可以在建立索引时指定索引的名称，使用方式是使用ensureIndex函数的第二个参数指定name键，如下图：为age键建立倒序索引，并指定名称为“\$index_age_-1”。
```shell
> db.person.ensureIndex({age:-1},{name:"$index_age_-1"})
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "test.system.users" }
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "test.person" }
{ "v" : 1, "key" : { "age" : 1 }, "name" : "age_1", "ns" : "test.person" }
{ "v" : 1, "key" : { "age" : -1 }, "name" : "$index_age_-1", "ns" : "test.person" }
>
```
###**4、唯一索引**
建立唯一索引是使用ensureIndex函数的第二个参数，指定unique键为true，如下图：为name键建立升序的唯一索引，并指定索引名字。
```shell
> db.person.ensureIndex({name:1},{name:"$index_name_1",unique:true})
> db.person.insert({name:"lisi"})
WriteResult({
        "nInserted" : 0,
        "writeError" : {
                "code" : 11000,
                "errmsg" : "E11000 duplicate key error index: test.person.$$inde
x_age_1 dup key: { : \"lisi\" }"
        }
})
>
```
这样建立唯一索引后，当再次插入一条文档，而该文档的name键的值已存在了就不能插入该文档了。
但是如果在为某个key建立唯一索引之前，该集合的所有文档中这个key的值**已经存在重复**的情况了，如果是在这种情况下为该key建立唯一索引，则要使用`ensureIndex`函数的第二个参数，指定`dropDups`键为`true`，如下图：指定`了dropDups`键为`true`后，它会删除在这个要建立索引的key上重复的数据，保留这个键第一次出现某个值的文档，**再次出现这个值的文档会直接被删掉**
```shell
> db.person.ensureIndex({name:-1},{unique:true,dropDups:true})
```
###**5、hint：在查询时强制使用指定的索引**
（前提是指定使用的索引必须是已经成功创建了的）
`hint`函数可以作为操作游标的函数调用，它会继续返回一个游标，一般情况下没有必要通过`hint`去强制使用某个索引，MongoDB的查询优化器能帮助使用最佳的索引去进行查询。如下图：
```shell
> db.person.find()
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1003, "age" : 19, "name" : "wangwu" }
>
> db.person.find().hint({age:1})
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1003, "age" : 19, "name" : "wangwu" }
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
>
```
##**二、索引管理**
###**1、删除索引**
在删除索引时可以指定要删除的索引的名字而执行精确删除，还可以使用*号来进行批量删除，shell命令为：`db.runCommand({dropIndexes:"集合名", index:"索引名或*号"})`，如下图：不能删除默认给_id键生成的索引。
```shell
> db.runCommand({dropIndexes:"person",index:"age_1"})
{ "nIndexesWas" : 4, "ok" : 1 }
>
```
除了这种使用命令的方式外，还可以直接使用在集合上：`db.集合名.dropIndexes()`，它会直接将该集合所有的索引全部删掉。
还可以使用集合函数：`db.集合名.dropIndex("索引名")`，它会删除该集合上指定名字的索引，注意使用这个函数的正确步骤应该是**先通过查询system.indexes确认索引的名称，然后再删除**，因为不是所有语言的数据库驱动都是按照前面介绍的方式去生成索引名称。
除此之外，还有一种删除索引的方式是将集合删掉，这样所有索引（包括键“_id”的唯一索引）、文档都会被删除。而上面介绍的的删除所有索引的方式都不会删除系统为键“_id”创建的唯一索引，且调用集合的remove函数，即使删除所有文档，也不会删除索引，当往集合中添加数据时，该索引还会起作用。
###**2、索引的构建**
是一个耗时耗资源的过程，并且在构建过程中会暂时锁表，数据库会阻塞所有的访问请求。为了不影响查询可以让索引的创建过程在后台执行，即指定ensureIndex函数的第二个参数的background键为true即可，表明在数据库服务空闲时来构建索引，如下：对于一个大数量的集合添加索引时应该启用这个参数，且即时启用了这个参数，构建索引仍会影响正常服务，但不会彻底阻塞数据库服务。

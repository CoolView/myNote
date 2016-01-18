# MongoDB 数据库，对象，集合


##**数据库**##
一个mongodb中可以建立多个数据库。

MongoDB的默认数据库为"db"，该数据库存储在data目录中。

在MongoDB中可以创建数据库，如果你想使用MongoDB，创建数据库不是必要的。

"show dbs" 命令可以显示所有数据的列表。
```shell
    C:\Users\bin>mongo
    MongoDB shell version: 3.0.2
    connecting to: test
    > show dbs
    admin  0.078GB
    db     0.078GB
    local  0.078GB
    test   0.078GB
    >
```
执行 "db" 命令可以显示当前数据库对象或集合。
```shell
> db
test
>
```
运行"use"命令，可以连接到一个指定的数据库。
```shell
> use local
switched to db local
> 
```
<font color="red">注意：</font>
>  
1. 数据库名称可以是任何字符，但是不能包含空字符串，点号（.），或者" "。
2. "system" 作为系统保留字符串不能作为数据库名。
3. 数据库名不能包含 "$"。

## **文档** ##
文档是mongodb中的最核心的概念，是其核心单元，我们可以将文档类比成关系型数据库中的每一行数据。

**<font color="red">多个键及其关联的值有序的放置在一起就是文档</font>**。在mongodb中使用一种类json的bson存储数据。

MongoDB中对文档的区分包括：**键值对的顺序，键和值的类型，键和值的大小写**。在两个文档中如果这三者有任何一个不一致，这两个文档就不相同。文档中键值对的“值”的数据类型甚至还可以是整个嵌入的文档，“键”则只能是字符串。

文档例子如下：
```shell
 { name : "zhang" }
```
通常，"object（对象）" 术语是指一个文件。

文件类似于一个RDBMS的记录。

我们可以对集合（collection）进行插入，更新和删除操作。

下表将帮助您更容易理解Mongo中的一些概念：
<table>
    <tr>
        <th>RDBMS </th>
        <th>MongoDB </th>
    </tr>
    <tr>
        <td>Table（表）</td>
        <td>Collection（集合）</td>
    </tr>
    <tr>
        <td>Column（栏）</td>
        <td>Key（键）</td>
    </tr>
    <tr>
        <td>Value（值）</td>
        <td>Value（值）</td>
    </tr>
    <tr>
        <td>Records / Rows（记录/列）</td>
        <td>Document / Object（文档/对象）</td>
    </tr>
</table>

下表为MongoDB中常用的几种数据类型。
<table>
    <tr>
        <th>数据类型 </th>
        <th>描述 </th>
    </tr>
    <tr>
        <td>string（字符串）</td>
        <td>可以是一个空字符串或者字符组合。</td>
    </tr>
    <tr>
        <td>integer（整型）</td>
        <td>整数。</td>
    </tr>
    <tr> <td>boolean（布尔型）</td> <td>逻辑值 True 或者 False。</td> </tr>
    <tr> <td>double</td> <td>双精度浮点型</td> </tr>
    <tr> <td>null</td> <td>不是0，也不是空。</td> </tr>
    <tr> <td>array</td> <td>数组：一系列值</td> </tr>
    <tr> <td>object</td> <td>对象型，程序中被使用的实体。可以是一个值，变量，函数，或者数据结构。</td> </tr>
    <tr> <td>timestamp</td> <td>timestamp存储为64为的值，只运行一个mongod时可以确保是唯一的。前32位保存的是UTC时间，单位是秒，后32为是在这一秒内的计数值，从0开始，每新建一个MongoTimestamp对象就加一。</td> </tr>
    <tr> <td>Internationalized Strings</td> <td>UTF-8 字符串。</td> </tr>
    <tr> <td>Object IDs</td> <td>在mongodb中的文档需要使用唯一的关键字_id来标识他们。几乎每一个mongodb文档都使用_id字段作为第一个属性（在系统集合和定容量集合（capped collection）中有一些例外）。_id值可以是任何类型，最常见的做法是使用ObjectId类型。</td> </tr>
</table>
## **集合**
集合就是一组文档的组合。如果将文档类比成数据库中的行，那么集合就可以类比成数据库的表。

在mongodb中的集合是无模式的，也就是说集合中存储的文档的结构可以是不同的，比如下面的两个文档可以同时存入到一个集合中：
```shell
{"name":"zhang"} {"Name":"zhang","sex":"nan"}
```
当第一个文档插入时，集合就会被创建。
## **capped collections**
Capped collections 就是固定大小的collection。

它有很高的性能以及队列过期的特性(过期按照插入的顺序). 有点和 "RRD" 概念类似。

Capped collections是高性能自动的维护对象的插入顺序。它非常适合类似记录日志的功能 和标准的collection不同，你必须要**显式的创建**一个capped collection， 指定一个collection的大小，单位是字节。collection的数据存储空间值提前分配的。

要注意的是指定的存储大小包含了数据库的头信息。
```shell
> db.createCollection("mycoll", {capped:true, size:100000})
{ "ok" : 1 }
>
```
- 在capped collection中，你能添加新的对象。
- 能进行更新，然而，对象不会增加存储空间。
- 如果增加，更新就会失败 。
- 数据库不允许进行删除。
```shell
> db.mycoll.insert({"name":"zhang"})
WriteResult({ "nInserted" : 1 })
> db.mycoll.remove({"name":"zhang"})
WriteResult({
        "nRemoved" : 0,
        "writeError" : {
                "code" : 20,
                "errmsg" : "cannot remove from a capped collection: test.mycoll"
        }
})
>
```
- 使用drop()方法删除collection所有的行。
- 注意: 删除之后，你必须显式的重新创建这个collection。
- 在32bit机器中，capped collection最大存储为1e9( 1X109)个字节。

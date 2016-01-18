# Shell中执行增

标签（空格分隔）： MongoDB

---

1、添加集合并插入一个文档：db.集合名.insert({...})
```
> db.person.insert({"name":"zhang","age":15})
WriteResult({ "nInserted" : 1 })
>
```
注意：MongoDB会自动为该文档加入一个“_id”的key，但是如果要插入的文档中已经包含了"_id"的key，则不会再自动添加了

以上执行insert时候传入的是一个对象，那么传入的是一个对象数组呢？如下图：如果插入时传入的是一个对象数组，那么会为该对象数组中的每一个对象插入一个文档。
```
> db.person.insert([{"name":"zhang","age":15},{"name":"wang","age":22}])
BulkWriteResult({
        "writeErrors" : [ ],
        "writeConcernErrors" : [ ],
        "nInserted" : 2,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})
> db.person.find()

{ "_id" : ObjectId("556bf3da361be67b8f01af25"), "name" : "zhang", "age" : 15 }
{ "_id" : ObjectId("556bf3da361be67b8f01af26"), "name" : "wang", "age" : 22 }
>
```
如果采用save方法遇到已经存在的"_id"，则不会报错，它其实是把已经存在的“_id”的那个文档进行更新；当然，如果要插入的文档的"_id"值还不存在则插入新文档就是，如下图：总结save函数就是如果该函数参数的那个文档包含“_id”键，则save函数其实会转调upsert，如果文档中不存在"_id"键，save函数默认就是向集合中插入一条文档
```
> db.person.find()
{ "_id" : 1002, "name" : "zhang", "age" : 15 }
> db.person.save({"_id":1002,"name":"wang","age":30})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : 1002, "name" : "wang", "age" : 30 }
>
```
###**插入文档数组**
定义变量 mydocuments 用于保存待插入的文档数组。
```
> var mydocuments =
     [
       {
         item: "ABC2",
         details: { model: "14Q3", manufacturer: "M1 Corporation"},
         stock: [ { size: "M", qty: 50 } ],
         category: "clothing"
       },
       {
         item: "MNO2",
         details: { model: "14Q3", manufacturer: "ABC Company" },
         stock: [ { size: "S", qty: 5 }, { size: "M", qty: 5 }, { size: "L",
qty: 1 } ],
         category: "clothing"
       },
       {
         item: "IJK2",
         details: { model: "14Q2", manufacturer: "M5 Corporation"},
         stock: [ { size: "S", qty: 5 }, { size: "L", qty: 1 } ],
         category: "houseware"
       }
     ];
```
传递数组 mydocuments 到方法 db.collection.insert() 执行批量插入。
```
db.inventory.insert( mydocuments );
```
该方法返回包含操作状态的 BulkWriteResult 对象。若插入成功将返回如下对象：
```
BulkWriteResult({
   "writeErrors" : [ ],
   "writeConcernErrors" : [ ],
   "nInserted" : 3,
   "nUpserted" : 0,
   "nMatched" : 0,
   "nModified" : 0,
   "nRemoved" : 0,
   "upserted" : [ ]
})
```
###**用 Bulk 插入多个文档**
2.6 新版功能.

MongoDB提供一个用于执行批量写入的 Bulk() API。以下操作步骤描述了如何使用 Bulk() API向MongoDB集合中插入一组文档。
	
1.初始化一个操作构建器 Bulk 。
给集合 inventory 初始化一个操作构建器 Bulk。
```
var bulk = db.inventory.initializeUnorderedBulkOp();
```
该操作返回一个无序的操作构建器，维护了所有待执行的操作列表。无序操作意味着MongoDB能够以平行方式执行，也可以用非确定性顺序的方式执行列表中的操作。如果当执行其中一个写操作时出现错误，MongoDB将继续执行列表中其他剩余的写操作。

你也可初始化一个有序的操作构建器；详见 [db.collection.initializeOrderedBulkOp()][1]。
	
2.向 bulk 对象中添加插入操作。
使用 Bulk.insert() 方法向 bulk 对象中添加两个插入操作。
```
bulk.insert(
   {
     item: "BE10",
     details: { model: "14Q2", manufacturer: "XYZ Company" },
     stock: [ { size: "L", qty: 5 } ],
     category: "clothing"
   }
);
bulk.insert(
   {
     item: "ZYT1",
     details: { model: "14Q1", manufacturer: "ABC Company"  },
     stock: [ { size: "S", qty: 5 }, { size: "M", qty: 5 } ],
     category: "houseware"
   }
);
```
	
3.执行批量操作。
调用 bulk 对象的 execute() 方法以执行 bulk 对象列表中的所有操作。
```
bulk.execute();
```
该方法返回包含操作状态的 BulkWriteResult 对象。若插入成功将返回如下对象：
```
BulkWriteResult({
   "writeErrors" : [ ],
   "writeConcernErrors" : [ ],
   "nInserted" : 2,
   "nUpserted" : 0,
   "nMatched" : 0,
   "nModified" : 0,
   "nRemoved" : 0,
   "upserted" : [ ]
})
```


  [1]: http://docs.mongoing.com/manual-zh/reference/method/db.collection.initializeOrderedBulkOp.html#db.collection.initializeOrderedBulkOp
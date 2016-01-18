# shell中执行更新

标签（空格分隔）： MongoDB

---
###参数
**update()**函数接受以下四个参数：

**criteria** : update的查询条件，类似sql update查询内where后面的。
**objNew** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
**upsert** : 这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
**multi** : mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。

###**1、强硬的文档替换式更新**
此种更新操作类似于把之前的老文档删除，然后替换为更新的文档，也就是用新文档替换老文档，如下图：原文档中根本不存在age这个key，但是执行替换式更新后，不管老文档存在哪些key，更新最后都仅仅只会存在新的文档中的那些key（原文档的_id还会存在）。
```
> db.person.find()
{ "_id" : ObjectId("556c0b48361be67b8f01af2c"), "name" : "zhang" }
>
> db.person.update({name:"zhang"},{age:4})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : ObjectId("556c0b48361be67b8f01af2c"), "age" : 4 }
>
```
这个时候如果是真的只需要更新name键的值，可以使用如下的方式：但是这种方式要注意一个问题，就是变量p必须通过**findOne()**去获得，如果通过find()方法获得，则后面的update方法会报错；而且因为findOne方法仅会返回查询文档匹配的一条集合文档，使用这种方式做update时，此处update满足更新条件的文档可能不止一条，那么更新就会失败，因为会出现集合中"_id"键值重复的现象。
```
> db.person.find()
{ "_id" : ObjectId("556c0b48361be67b8f01af2c"), "age" : 4 }
>
> var p = db.person.findOne()
> p.age = 14;
14
> db.person.update({"_id" : ObjectId("556c0b48361be67b8f01af2c")},p)
>
> db.person.find()
{ "_id" : ObjectId("556c0b48361be67b8f01af2c"), "age" : 14 }
>
```
**不可以更新“_id”这个key的值，而不管更新后的“_id”值是否已经存在**
###**2、使用修改器进行局部更新**
####**2.1 \$set修改器**
`{$set:{key:value}}`，如果满足更新条件的文档中存在\$set修改器中的key时，则进行更新，否则进行添加，如下：还是只会对满足条件的第一个文档进行更新
```
> db.person.find()
{ "_id" : 1001, "name" : "eee" }
{ "_id" : 1002, "name" : "eee" }
>
> db.person.update({name:"eee"},{$set:{name:"aaa",age:23}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "age" : 23 }
{ "_id" : 1002, "name" : "eee" }
>
```
####**2.2 \$inc修改器**
`{\$inc:{key:value}}`，仅适用于数字类型，可以为指定的key对应的数字类型的值进行加减操作，如下：
```
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "age" : 23 }
{ "_id" : 1002, "name" : "eee" }
>
> db.person.update({name:"aaa"},{$inc:{age:2}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "age" : 25 }
{ "_id" : 1002, "name" : "eee" }
>
> db.person.update({name:"aaa"},{$inc:{age:-2}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "age" : 23 }
{ "_id" : 1002, "name" : "eee" }
>
```
####**2.3 \$unset修改器**
`{\$unset:{key:value}}`，删除指定的key，如下：
```
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "age" : 23 }
{ "_id" : 1002, "name" : "eee" }
>
> db.person.update({name:"aaa"},{$unset:{age:23}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa" }
{ "_id" : 1002, "name" : "eee" }
>
```
####**2.4 \$push修改器**
`{\$push:{key:value}}`，如果指定的**key是数组**，则往该数组中追加新的元素；如果指定的key不是数组，则中断操作；如果不存在指定的key，则创建，且类型为数组类型，并加入新的元素；如下：
```
> db.person.find()
{ "_id" : 1001, "name" : "aaa" }
{ "_id" : 1002, "name" : "eee" }
>
> db.person.update({name:"aaa"},{$push:{name:"java"}})
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 0,
        "nModified" : 0,
        "writeError" : {
                "code" : 16837,
                "errmsg" : "The field 'name' must be an array but is of type Str
ing in document {_id: 1001.0}"
        }
})
> db.person.update({name:"aaa"},{$push:{books:"java"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : 1002, "name" : "eee" }
{ "_id" : 1001, "name" : "aaa", "books" : [ "java" ] }
> db.person.update({name:"aaa"},{$push:{books:"MongoDB"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : 1002, "name" : "eee" }
{ "_id" : 1001, "name" : "aaa", "books" : [ "java", "MongoDB" ] }
>
```
####**2.5 \$pushAll修改器**
`{\$pushAll:{key:value}}`，与$push一样，只是它可以一次性批量加入一个数组中的所有元素，如下图：
```
> db.person.find()
{ "_id" : 1002, "name" : "eee" }
{ "_id" : 1001, "name" : "aaa", "books" : [ "java", "MongoDB" ] }
>
> db.person.update({name:"aaa"},{$pushAll:{books:["JavaEE","JSP"]}})
> db.person.find()
{ "_id" : 1002, "name" : "eee" }
{ "_id" : 1001, "name" : "aaa", "books" : [ "java", "MongoDB", "JavaEE", "JSP" ]
 }
>
```
####**2.6 \$addToSet修改器**
`{\$addToSet:{key:value}}`，与`$push`一样，只是当目标数组不存在该元素时才加入，如下图：
```
> db.person.find()
{ "_id" : 1002, "name" : "eee" }
> db.person.update({name:"eee"},{$addToSet:{name:"java"}})
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 0,
        "nModified" : 0,
        "writeError" : {
                "code" : 16837,
                "errmsg" : "Cannot apply $addToSet to a non-array field. Field n
amed 'name' has a non-array type String in the document _id: 1002.0"
        }
})
>  db.person.update({name:"eee"},{$addToSet:{books:"Java"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.person.find()
{ "_id" : 1002, "name" : "eee", "books" : [ "Java" ] }
>
> db.person.update({name:"eee"},{$addToSet:{books:"Java"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 0 })
> db.person.find()
{ "_id" : 1002, "name" : "eee", "books" : [ "Java" ] }
>
```
####**2.7 \$pop修改器**
`{$pop:{key:value}}`，从指定数组的头或尾**删除**一个元素，从头删除一个元素用小于0的值，从尾删除一个元素用大于0的值，如下图：
```
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "java", "MongoDB", "JavaEE", "JSP","Spring" ] }
>
> db.person.update({_id:1001},{$pop:{books:-1}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "MongoDB", "JavaEE", "JSP", "Spring] }
>
> db.person.update({_id:1001},{$pop:{books:-2}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "JavaEE", "JSP", "Spring" ] }
>
> db.person.update({_id:1001},{$pop:{books:1}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "JavaEE", "JSP" ] }
>
> db.person.update({_id:1001},{$pop:{books:2}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "JavaEE" ] }
>
```
####**2.8 \$pull修改器**
`{\$pull:{key:value}}`，从指定数组中删除一个被指定的值，如下：
```
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "JavaEE" ] }
> db.person.update({_id:1001},{$pull:{books:"JavaEE"}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ ] }
>
```
####**2.9 \$pullAll修改器**
`{\$pullAll:{key:value}}`，从指定数组中一次性删除多个被指定的值，如下：
```
> db.person.find()
{ "_id" : 1002, "name" : "eee", "books" : [ "Java", "JavaEE" ] }
>
> db.person.update({_id:1002},{$pullAll:{books:["JavaEE","Java"]}})
> db.person.find()
{ "_id" : 1002, "name" : "eee", "books" : [ ] }
>
```
####**2.10 数组定位器`$`**
如果数组中有多个元素，需要对其中一部分进行更新，则可以使用定位器`$`。如下图：可以发现它还是只会对该文档中books那个数组中满足条件的第一个元素其作用，且注意：当使用了`books.price`这种方式时，一定要加上引号，不然会报错。
```
> db.person.find()
{ "_id" : 1002, "books" : [ { "name" : "JSP", "price" : 23.5 }, 
                { "name" : "JAVA", "price" : 45.5 }, 
                { "name" : "Spring", "price" : 45.5 } ] }
>
> db.person.update({"books.price":45.5},{$set:{"books.$.author":"abc"}})
> db.person.find()
{ "_id" : 1002, "books" : [ { "name" : "JSP", "price" : 23.5 }, 
            { "name" : "JAVA", "price" : 45.5, "author" : "abc" },
            { "name" : "Spring", "price" : 45.5 } ] }
>
```
**如果是知道数组元素索引，还可以通过索引的方式，比如把上面的books.$.author改为：books.0.author。**
####2.11 **`$addToSet`与`$each`结合完成批量数组更新**
如下：`$each`会循环后面的数组，把每个元素值进行`$addToSet`操作。
```
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "Java" ] }
>
> db.person.update({_id:1001},{$addToSet:{books:{$each:["Java","JSP"]}}})
> db.person.find()
{ "_id" : 1001, "name" : "aaa", "books" : [ "Java", "JSP" ] }
>
```
####**2.12 `$push`与`$inc`结合使用**
比如要往文档的books这个key（数组）中加入一个元素，同时该文档的books_size这个key的大小加1，如下图：这个方式和修改器`$addToSet`没法配合使用，因为无法判断这个元素是否添加到了数组中
```
> db.person.find()
{ "_id" : 1002, "books" : [ "Java", "MongoDB" ], "books_size" : 2, "name" : "aaa" }
>
> db.person.update({_id:1002},{$push:{books: "JSP"}, $inc:{books_size:1}})
> db.person.find()
{ "_id" : 1002, "books" : [ "Java", "MongoDB", "JSP" ], "books_size" : 3, "name": "aaa" }
>
```


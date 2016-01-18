# shell中执行查询

标签（空格分隔）： MongoDB

---
[toc]
### **查询集合中所有文档**
db.集合名.find()
### **查询集合中第一个文档**
db.集合名.findOne()
### **指定查询条件**
第一个参数就是指定查询条件，注意：查询文档中“键值对”中的值必须为常量
### **查询全部文档**
db.集合名.find({})，如果查询文档{}中包含了多个键值对，则是AND的关系。
```
> db.pers.find()
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1002, "name" : "bbb", "age" : 12 }
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1004, "name" : "ddd", "age" : 15 }
{ "_id" : 1005, "name" : "eee", "age" : 22 }
>
> db.pers.find({})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1002, "name" : "bbb", "age" : 12 }
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1004, "name" : "ddd", "age" : 15 }
{ "_id" : 1005, "name" : "eee", "age" : 22 }
>
```
### **五种基本的查询条件**
\$lt(<)、\$lte(<=)、\$gt(>)、\$gte(>=)、\$ne(!=)，如下图：从第一个实验可以发现，当对某个key，该key的value中同时指定了**多个查询条件，这些查询条件的关系是“与”关系**；但是从第二个和第三个实现中可以发现，当对某个key，分开指定了多次查询条件，则对该key的查询条件是以最后一个查询条件为准。
```
> db.pers.find({age:{$gt:13,$lt:22}})
{ "_id" : 1004, "name" : "ddd", "age" : 15 }
>
> db.pers.find({age:{$gt:13},age:{$lt:22}})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1002, "name" : "bbb", "age" : 12 }
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1004, "name" : "ddd", "age" : 15 }
>
```
### **包含与不包含：\$in、\$nin**
都只能作用于数组，而不能作用于其他类型，如下图：
```
> db.pers.find({age:{$in:[9,12]}})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1002, "name" : "bbb", "age" : 12 }
>
> db.pers.find({age:{$nin:[9,12]}})
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1004, "name" : "ddd", "age" : 15 }
{ "_id" : 1005, "name" : "eee", "age" : 22 }
>
```
###**or查询：\$or**
注意最外面是or查询器，or查询器的value是一个数组，里面是很多的查询条件，数组中每个查询条件就是or的关系。使用这个条件操作符有一个最佳实践是：将最宽松的条件放在前面，这样可以加快文档匹配速度
```
> db.pers.find({$or:[{name:{$in:["aaa","bbb"]}},{age:{$gt:15}}]})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1002, "name" : "bbb", "age" : 12 }
{ "_id" : 1005, "name" : "eee", "age" : 22 }
>
```
###**null查询**
会查询出没有这个key的记录，如果需要过滤掉这种文档，需要另外一个条件操作符\$exists，指明这个键必须存在，如下如：此时“=null”的判断只能通过{"\$in"：[null]}来实现了
```
> db.pers.find()
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1002, "age" : 12, "name" : "bbb", "sex" : null }
{ "_id" : 1004, "age" : 12, "name" : "bbb", "sex" : "M" }
{ "_id" : 1005, "age" : 12, "name" : "bbb", "sex" : "M" }
>
> db.pers.find({sex: {$in: [null]}})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1002, "age" : 12, "name" : "bbb", "sex" : null }
>
> db.pers.find({sex:null})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1002, "age" : 12, "name" : "bbb", "sex" : null }
>
> db.pers.find({sex: {$in: [null], $exists: true}})
{ "_id" : 1002, "age" : 12, "name" : "bbb", "sex" : null }
>
```
###**正则查询**
Shell中写正则表达式的方式和JavaScript的一致，写在一对“ // ”之间的就是正则表达式。查询name这个key对应的value中包含有字母a的文档，如下图
```
> db.pers.find({name: /a/i})
{ "_id" : 1001, "name" : "aaa", "age" : 9 }
>
```
**MongoDB支持正则表达式这种数据类型**，对于这种键值对，正则表达式也可以匹配成功，如下图：还可以发现正则匹配不区分大小写。但注意正则表达式的匹配必须是完全匹配，即正则表达式的写法完全相同才可以匹配成功。MongoDB可以为前缀型的正则表达式（/^abc/i）查询使用索引，所以这种前缀型的正则表式查询速度会很快。
```
> db.preso.find()
{ "_id" : 1001, "age" : 9, "name" : "abc" }
{ "_id" : 1002, "age" : 10, "name" : "aBc" }
{ "_id" : 1003, "age" : 10, "name" : /abc/i }
>
> db.preso.find({name:/abc/i})
{ "_id" : 1001, "age" : 9, "name" : "abc" }
{ "_id" : 1002, "age" : 10, "name" : "aBc" }
{ "_id" : 1003, "age" : 10, "name" : /abc/i }
>
```
###**\$not：可以用到任何地方进行取反的操作**
与\$nin的区别就在于\$nin只能用在数组上，如下图：
```
> db.pers.find({name:{$not: /a/i}})
{ "_id" : 1003, "name" : "ccc", "age" : 13 }
{ "_id" : 1002, "age" : 12, "name" : "bbb", "sex" : null }
{ "_id" : 1004, "age" : 12, "name" : "bbb", "sex" : "M" }
{ "_id" : 1005, "age" : 12, "name" : "bbb", "sex" : "M" }
>
```
###**\$mod**
使用格式为 {"键"：{"\$mod"：[num1, num2]}}，查询“键”的值对num1取余，如果余等于num2，则整条文档符合条件，如下：
```
> db.preso.find()
{ "_id" : 1001, "age" : 9, "name" : "abc" }
{ "_id" : 1002, "age" : 10, "name" : "aBc" }
{ "_id" : 1003, "age" : 10, "name" : /abc/i }
>
> db.preso.find({age:{$mod:[2,0]}})
{ "_id" : 1002, "age" : 10, "name" : "aBc" }
{ "_id" : 1003, "age" : 10, "name" : /abc/i }
>
```
###**查询指定key：第二个参数就是指定要查询的key**
想要查询出哪个key，就将其对应的value置为非0，默认情况下会将_id也查询出来，可以将其对应的value置为0，就不会查询出来了
```
> db.preso.find({},{age:1,age:0})
{ "_id" : 1001, "name" : "abc" }
{ "_id" : 1002, "name" : "aBc" }
{ "_id" : 1003, "name" : /abc/i }
>
> db.preso.find({},{age:1,_id:0})
{ "age" : 9 }
{ "age" : 10 }
{ "age" : 10 }
```
###**数组**
考虑集合 inventory 包含如下文档：
```
{ _id: 5, type: "food", item: "aaa", ratings: [ 5, 8, 9 ] }
{ _id: 6, type: "food", item: "bbb", ratings: [ 5, 9 ] }
{ _id: 7, type: "food", item: "ccc", ratings: [ 9, 5, 8 ] }
```
####**数组精确匹配**
要指定数组相等匹配，使用查询文档 { <field>: <value> } 其中 <value> 是匹配的数组。数组的相等匹配要求数组字段与指定的匹配数组 <value> 完全 相符，包括数组元素的顺序。

下面示例将筛选出 ratings 字段为数组，包含 5， 8， 9 三个元素并且元素顺序符合该顺序的所有文档：
```
db.inventory.find( { ratings: [ 5, 8, 9 ] } )
```
此操作将返回如下文档：
```
{ "_id" : 5, "type" : "food", "item" : "aaa", "ratings" : [ 5, 8, 9 ] }
```
####**匹配数组元素**
相等匹配可以指定数组中单一元素进行匹配。这些相等匹配将匹配包含至少一个元素等于指定值的数组。

下面示例查询数组字段 ratings 中元素之一为 5 的所有文档。
```
db.inventory.find( { ratings: 5 } )
```
操作将返回如下文档：
```
{ "_id" : 5, "type" : "food", "item" : "aaa", "ratings" : [ 5, 8, 9 ] }
{ "_id" : 6, "type" : "food", "item" : "bbb", "ratings" : [ 5, 9 ] }
{ "_id" : 7, "type" : "food", "item" : "ccc", "ratings" : [ 9, 5, 8 ] }
```
####**匹配数组中特定元素**
使用 `dot notation` 可以在数组特定的索引或位置指定相等匹配的元素。

在下面示例中，查询使用 `dot notation` 匹配数组字段 ratings 的第一个元素为 5 的所有文档：
```
db.inventory.find( { 'ratings.0': 5 } )
```
操作将返回如下文档：
```
{ "_id" : 5, "type" : "food", "item" : "aaa", "ratings" : [ 5, 8, 9 ] }
{ "_id" : 6, "type" : "food", "item" : "bbb", "ratings" : [ 5, 9 ] }
```
####**为数组元素指定复合条件**
#####**单元素满足条件**
使用 `$elemMatch`操作符为数组元素指定**复合条件**，以查询数组中至少一个元素满足所有指定条件的文档。

下面示例查询 ratings 数组中至少一个元素大于 `($gt) 5` 且小于 `($lt) 9` 的文档：
```
db.inventory.find( { ratings: { $elemMatch: { $gt: 5, $lt: 9 } } } )
```
操作返回如下文档，这些文档的 ratings 中包含元素 8，满足条件：
```
{ "_id" : 5, "type" : "food", "item" : "aaa", "ratings" : [ 5, 8, 9 ] }
{ "_id" : 7, "type" : "food", "item" : "ccc", "ratings" : [ 9, 5, 8 ] }
```
#####**满足条件的元素组合**
下面示例将查询 ratings 中包含某些元素组合满足查询条件的文档；比如，一个元素满足大于 5 的条件，另外元素满足小于 9 的条件，或一个元素同时满足两个条件；
```
db.inventory.find( { ratings: { $gt: 5, $lt: 9 } } )
```
操作将返回如下文档：
```
{ "_id" : 5, "type" : "food", "item" : "aaa", "ratings" : [ 5, 8, 9 ] }
{ "_id" : 6, "type" : "food", "item" : "bbb", "ratings" : [ 5, 9 ] }
{ "_id" : 7, "type" : "food", "item" : "ccc", "ratings" : [ 9, 5, 8 ] }
```
####**给文档数组指定复合条件**
#####**单元素满足条件**
使用 `$elemMatch`操作符为内嵌文档数组指定复合条件，以查询数组中只是一个内嵌文档满足所有指定添加的文档。

下面示例查询 memos 数组中至少有一个内嵌文档同时包含 memo 和 by 字段并且值分别为 'on time' 与 'shipping' 的所有文档：
```
db.inventory.find( { memos : { $elemMatch : { memo: 'on time', by: 'shipping'} } } )
```
此操作将返回如下文档：
```
{
   _id: 100,
   type: "food",
   item: "xyz",
   qty: 25,
   price: 2.5,
   ratings: [ 5, 8, 9 ],
   memos: [ { memo: "on time", by: "shipping" }, { memo: "approved", by: "billing" } ]
}
```
#####**满足条件的元素组合**
下面示例查询 memos 数组中包含某些元素组合满足查询条件的文档：例如，其中一个元素满足字段 memo 值等于 'on time' 条件，另外一个元素满足字段 by 值等于 'shipping' ，或一个元素同时满足两个条件：
```
db.inventory.find(
  {
    'memos.memo': 'on time',
    'memos.by': 'shipping'
  }
)
```
查询返回如下文档：
```
{
  _id: 100,
  type: "food",
  item: "xyz",
  qty: 25,
  price: 2.5,
  ratings: [ 5, 8, 9 ],
  memos: [ { memo: "on time", by: "shipping" }, { memo: "approved", by: "billing" } ]
}
{
  _id: 101,
  type: "fruit",
  item: "jkl",
  qty: 10,
  price: 4.25,
  ratings: [ 5, 9 ],
  memos: [ { memo: "on time", by: "payment" }, { memo: "delayed", by: "shipping" } ]
}
```









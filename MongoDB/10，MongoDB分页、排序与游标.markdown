# MongoDB分页、排序与游标

标签（空格分隔）： MongoDB

---

一、分页
1、limit返回指定条数的数据
```
> db.person.find()
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
>
> db.person.find().limit(3)
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
>
```
2、skip指定跨度
```
> db.person.find().skip(3)
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
>
```
3、limit与skip结合，进行分页
```
> db.person.find().skip(0).limit(2)
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
>
> db.person.find().skip(2).limit(2)
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
>
> db.person.find().skip(4).limit(2)
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
>
```
二、排序
1、sort排序：指定排序的key，大于0为升序，小于0为降序。如果指定了多个排序键，比如`{age:1,name:-1}`，则是先按age升序排列，对于age键相等的文档，则按name降序排列。
```
> db.person.find().sort({age:1})
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
>
> db.person.find().sort({age:-1})
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
>
```
2、排序的先后：从下图中可以发现，使用`sort`，始终是先对满足查询条件而查询到的结果集进行排序，然后再进行选取指定的条数或者是跳过指定的条数。它们的关系就是：在数据库服务器端，先执行`sort`，然后在排好序的文档上执行`skip`，最后按照`limit`设定的最大数量返回文档子集即可。
```
> db.person.find().limit(2).sort({age:1})
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
>
> db.person.find().sort({age:1}).limit(2)
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1003, "name" : "wangwu", "age" : 18 }
>
> db.person.find().skip(2).sort({age:1})
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
>
> db.person.find().sort({age:1}).skip(2)
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
>
```
3、排序的优先级：由于MongoDB的key可以存储不同类型的数据，所以排序是有优先级的，如下图：
```
> db.person.find()
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
{ "_id" : 1003, "age" : "十八岁", "name" : "wangwu" }
>
> db.person.find().sort({age:1})
{ "_id" : 1004, "name" : "zhaoliu", "age" : 17 }
{ "_id" : 1001, "name" : "lisi", "age" : 20 }
{ "_id" : 1002, "name" : "zhangsan", "age" : 21 }
{ "_id" : 1006, "name" : "zhouba", "age" : 23 }
{ "_id" : 1005, "name" : "sunqi", "age" : 24 }
{ "_id" : 1003, "age" : "十八岁", "name" : "wangwu" }
>
```
**优先级从高到低依次为**：最小值、null、数字、字符串、对象/文档、数组、二进制、对象ID、布尔、日期、时间戳、正则、最大值。

三、游标
1、如下图所示，查询出某个文档中某个key（假设类型是数组）的元素个数，使用find函数的返回值是一个游标，所以需要用到游标遍历的方式来遍历查询出来的每个文档，而findOne就可以直接输出。
```
> db.person1.find()
{ "_id" : 1002, "name" : "bbb", "books" : [ "JS" ] }
{ "_id" : 1001, "name" : "aaa", "books" : [ "JSP", "JAVA" ] }
>
> var iter = db.person1.find({name:"aaa"})
> while(iter.hasNext()) {
... var obj = iter.next();
... print(obj.books.length);
... }
2
>
> var iter = db.person1.findOne({name:"aaa"})
> iter.books.length
2
>
```
2、find()函数返回一个游标。如果在调用find函数时，不保存返回值，其会自动递归find返回的游标，将前20条数据展示在shell中（输入“it”会继续显示下20条），
```
{ "_id" : 17, "age" : 17 }
{ "_id" : 18, "age" : 18 }
{ "_id" : 19, "age" : 19 }
Type "it" for more
> it
{ "_id" : 20, "age" : 20 }
{ "_id" : 21, "age" : 21 }
{ "_id" : 22, "age" : 22 }
```
3、游标到底部就会释放资源，不能再次读取，只能读一遍

4、游标的销毁条件：

 - 客户端发来销毁信息。
 - 游标迭代完毕。
 - 默认情况下，游标超过10分钟没用也会被清除。

5、当获得游标后，客户端可以通过对游标进行一些设置就能对查询结果进行有效地控制，如可以限制查询得到的结果数量、跳过部分结果、或对结果集按任意键进行排序等，也就是先对游标进行处理后，再让访问数据库的动作按照指定的意愿发生。比如上面分页和排序中使用的limit()、skip()、sort()等函数，为什么这些函数可以进行链式调用呢？就是因为操作游标的函数返回的都是游标。
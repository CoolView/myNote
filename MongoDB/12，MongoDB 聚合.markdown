# MongoDB 聚合


---

1、count函数：查询文档数，如下图：
```shell
> db.person.find().count()
6
>
```
2、distinct：去重，用法：`db.runCommand({distinct:"集合名", key:"查询的键"})`，如下图：
```shell
> db.runCommand({distinct:"person",key:"age"})
```
3、group：分组，语法如下：首先会按照key指定的键进行分组，每组的每一个文档都要执行`$reduce`指定的方法，该方法接收2个参数，一个是组内本条文档，一个是累加器数据。
```shell
db.runCommand({group:{  
    ns: 集合名,  
    key: 分组的键,  
    initial: 初始化累加器,  
    $reduce: 组分解器,  
    condition: 条件,  
    finalize: 组完成器  
}})  
```
集合中的数据如下：
```shell
{
   _id: ObjectId(7df78ad8902c)
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by_user: 'w3cschool.cc',
   url: 'http://www.w3cschool.cc',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
},
{
   _id: ObjectId(7df78ad8902d)
   title: 'NoSQL Overview', 
   description: 'No sql database is very fast',
   by_user: 'w3cschool.cc',
   url: 'http://www.w3cschool.cc',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 10
},
{
   _id: ObjectId(7df78ad8902e)
   title: 'Neo4j Overview', 
   description: 'Neo4j is no sql database',
   by_user: 'Neo4j',
   url: 'http://www.neo4j.com',
   tags: ['neo4j', 'database', 'NoSQL'],
   likes: 750
},
```
现在我们通过以上集合计算每个作者所写的文章数，使用aggregate()计算结果如下：
```shell
> db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
{
   "result" : [
      {
         "_id" : "w3cschool.cc",
         "num_tutorial" : 2
      },
      {
         "_id" : "Neo4j",
         "num_tutorial" : 1
      }
   ],
   "ok" : 1
}
>
```
以上实例类似sql语句： select by_user, count(*) from mycol group by by_user

在上面的例子中，我们通过字段by_user字段对数据进行分组，并计算by_user字段相同值的总和。

下表展示了一些聚合的表达式:

<table class="reference"> 
    <tbody>
    <tr>
        <th style="width:10%;">表达式</th>
        <th style="width:50%">描述</th>
        <th>实例</th></tr>    
    <tr>
        <td>$sum</td>
        <td>计算总和。</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$sum : "\$likes"}}}])</td>
    </tr> 
    <tr>
        <td>\$avg</td>
        <td>计算平均值</td>
        <td>db.mycol.aggregate([{$group : {_id : "\$by_user", num_tutorial : {\$avg : "\$likes"}}}])</td>
    </tr> 
    <tr>
        <td>$min</td>
        <td>获取集合中所有文档对应值得最小值。</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$min : "\$likes"}}}])</td>
    </tr> 
    <tr>
        <td>$max</td>
        <td>获取集合中所有文档对应值得最大值。</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$max : "\$likes"}}}])</td>
    </tr> 
    <tr>
        <td>$push</td>
        <td>在结果文档中插入值到一个数组中。</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", url : {\$push: "\$url"}}}])</td>
    </tr> 
    <tr>
        <td>$addToSet</td>
        <td>在结果文档中插入值到一个数组中，但不创建副本。</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", url : {\$addToSet : "\$url"}}}])</td>
    </tr> 
    <tr>
        <td>$first</td>
        <td>根据资源文档的排序获取第一个文档数据。</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", first_url : {\$first : "\$url"}}}])</td>
    </tr> 
    <tr>
        <td>$last</td>
        <td>根据资源文档的排序获取最后一个文档数据</td>
        <td>db.mycol.aggregate([{\$group : {_id : "\$by_user", last_url : {\$last : "\$url"}}}])</td>
    </tr> 
</tbody>
</table>


##管道的概念
管道在Unix和Linux中一般用于将当前命令的输出结果作为下一个命令的参数。

MongoDB的聚合管道将MongoDB文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。

这里我们介绍一下聚合框架中常用的几个操作：

- \$project：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。
- \$match：用于过滤数据，只输出符合条件的文档\。$match使用MongoDB的标准查询操作。
- \$limit：用来限制MongoDB聚合管道返回的文档数。
- \$skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。
- \$unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
- \$group：将集合中的文档分组，可用于统计结果。
- \$sort：将输入文档排序后输出。
- \$geoNear：输出接近某一地理位置的有序文档。
管道操作符实例

1、\$project实例
```shell
db.article.aggregate(
    { $project : {
        title : 1 ,
        author : 1 ,
    }}
 );
```
 
这样的话结果中就只还有_id,tilte和author三个字段了，默认情况下_id字段是被包含的，如果要想不包含_id话可以这样:
```shell
db.article.aggregate(
    { $project : {
        _id : 0 ,
        title : 1 ,
        author : 1
    }});
```
2.$match实例
```shell
db.articles.aggregate( [
                { $match : { score : { $gt : 70, $lte : 90 } } },
                { $group: { _id: null, count: { $sum: 1 } } }
] );
```
\$match用于获取分数大于70小于或等于90记录，然后将符合条件的记录送到下一阶段\$group管道操作符进行处理。

3.$skip实例
```shell
db.article.aggregate(
    { $skip : 5 });
```
经过$skip管道操作符处理后，前五个文档被"过滤"掉。


# MySQL MongoDB 常用命令对比


![MongoDB](http://ww2.sinaimg.cn/large/81c3ce79jw1eho4qatgvaj20nd08374r.jpg)
<table>
	<tr>
		<th>MySQL</th>
		<th>MongoDB</th>
		<th>说明</th>
	</tr>
	<tr>
		<td>mysqld</td>
		<td>mongod</td>
		<td>服务器守护进程</td>
	</tr>
	<tr>
		<td>mysql</td>
		<td>mongo</td>
		<td>客户端工具</td>
	</tr>
	<tr>
		<td>mysqldump</td>
		<td>mongodump</td>
		<td>逻辑备份工具</td>
	</tr>
	<tr>
		<td>mysql</td>
		<td>mongorestore</td>
		<td>逻辑恢复工具</td>
	</tr>
	<tr>
		<td> </td>
		<td>db.repairDatabase()</td>
		<td>修复数据库</td>
		
	</tr>
	<tr>
		<td>mysqldump</td>
		<td>mongoexport</td>
		<td>数据导出工具</td>
	</tr>
	<tr>
		<td>source</td>
		<td>mongoimport</td>
		<td>数据导入工具</td>
	</tr>
	<tr>
		<td>grant * privileges on *.* to …</td>
		<td>Db.addUser()
		<br />
		Db.auth()</td>
		<td>新建用户并权限</td>
	</tr>
	<tr>
		<td>show databases </td>
		<td>show dbs </td>
		<td>显示库列表 </td>
	</tr>
	<tr>
		<td>Show tables </td>
		<td>Show collections </td>
		<td>显示表列表 </td>
	</tr>
	<tr>
		<td>Show slave status </td>
		<td>Rs.status </td>
		<td>查询主从状态 </td>
	</tr>
	<tr>
		<td>Create table users(a int, b int) </td>
		<td>db.createCollection("mycoll", {capped:true, 
		size:100000}) 另：可隐式创建表。 </td>
		<td>创建表</td>
	</tr>   

	<tr>
		<td>Create INDEX idxname ON users(name) </td>
		<td>db.users.ensureIndex({name:1}) </td>
		<td>创建索引 </td>
	</tr>
	<tr>
		<td>Create INDEX idxname ON users(name,ts DESC) </td>
		<td>db.users.ensureIndex({name:1,ts:-1}) </td>
		<td>创建索引 </td>
	</tr>
	<tr>
		<td>Insert into users values(1, 1) </td>
		<td>db.users.insert({a:1, b:1}) </td>
		<td>插入记录 </td>
	</tr>
	<tr>
		<td>Select a, b from users </td>
		<td>db.users.find({},{a:1, b:1}) </td>
		<td>查询表 </td>
	</tr>
	<tr>
		<td>Select * from users </td>
		<td>db.users.find() </td>
		<td>查询表 </td>
	</tr>
	<tr>
		<td>Select * from users where age=33 </td>
		<td>db.users.find({age:33}) </td>
		<td>条件查询 </td>
	</tr>
	<tr>
		<td>Select a, b from users where age=33 </td>
		<td>db.users.find({age:33},{a:1, b:1}) </td>
		<td>条件查询 </th>
	</tr>
	<tr>
		<td>select * from users where age &lt 33 </td>
		<td>db.users.find({'age':{\$lt:33}}) </td>
		<td>条件查询 </td>
	</tr>
	<tr>
		<td>select * from users where age &gt33 and age &lt=40 </td>
		<td>db.users.find({'age':{\$gt:33,\$lte:40}}) </td>
		<td>条件查询 </th>
	</tr>
</table>


<table>
	<tr>
		<td>
			select * from users where a=1 and b='q'
		</td>
		<td>db.users.find({a:1,b:'q'})</td>
		<td>
		<p>
			条件查询
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select * from users where a=1 or b=2
		</p></td>
		<td>
		<p>
			db.users.find( { $or : [ { a : 1 } , { b : 2 } ] } )
		</p></td>
		<td>
		<p>
			条件查询
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select * from users limit 1
		</p></td>
		<td>
		<p>
			db.users.findOne()
		</p></td>
		<td>
		<p>
			条件查询
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select * from users where name like "%Joe%"
		</p></td>
		<td>
		<p>
			db.users.find({name:/Joe/})
		</p></td>
		<td>
		<p>
			模糊查询
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select * from users where name like "Joe%"
		</p></td>
		<td>
		<p>
			db.users.find({name:/^Joe/})
		</p></td>
		<td>
		<p>
			模糊查询
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select count(1) from users
		</p></td>
		<td>
		<p>
			Db.users.count()
		</p></td>
		<td>
		<p>
			获取表记录数
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select count(1) from users where age&gt;30
		</p></td>
		<td>
		<p>
			db.users.find({age: {'$gt': 30}}).count()
		</p></td>
		<td>
		<p>
			获取表记录数
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select DISTINCT last_name from users
		</p></td>
		<td>
		<p>
			db.users.distinct('last_name')
		</p></td>
		<td>
		<p>
			去掉重复值
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select * from users ORDER BY name
		</p></td>
		<td>
		<p>
			db.users.find().sort({name:-1})
		</p></td>
		<td>
		<p>
			排序
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			select * from users ORDER BY name DESC
		</p></td>
		<td>
		<p>
			db.users.find().sort({name:-1})
		</p></td>
		<td>
		<p>
			排序
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			EXPLAIN select * from users where z=3
		</p></td>
		<td>
		<p>
			db.users.find({z:3}).explain()
		</p></td>
		<td>
		<p>
			获取存储路径
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			update users set a=1 where b='q'
		</p></td>
		<td>
		<p>
			db.users.update({b:'q'}, {$set:{a:1}}, false, true)
		</p></td>
		<td>
		<p>
			更新记录
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			update users set a=a+2 where b='q'
		</p></td>
		<td>
		<p>
			db.users.update({b:'q'}, {$inc:{a:2}}, false, true)
		</p></td>
		<td>
		<p>
			更新记录
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			delete from users where z="abc"
		</p></td>
		<td>
		<p>
			db.users.remove({z:'abc'})
		</p></td>
		<td>
		<p>
			删除记录
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db. users.remove()
		</p></td>
		<td>
		<p>
			删除所有的记录
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			drop database IF EXISTS test;
		</p></td>
		<td>
		<p>
			use test
		</p>
		<p>
			db.dropDatabase()
		</p></td>
		<td>
		<p>
			删除数据库
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			drop table IF EXISTS test;
		</p></td>
		<td>
		<p>
			db.mytable.drop()
		</p></td>
		<td>
		<p>
			删除表/collection
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.addUser(‘test’, ’test’)
		</p></td>
		<td>
		<p>
			添加用户
		</p>
		<p>
			readOnly--&gt;false
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.addUser(‘test’, ’test’, true)
		</p></td>
		<td>
		<p>
			添加用户
		</p>
		<p>
			readOnly--&gt;true
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.addUser("test","test222")
		</p></td>
		<td>
		<p>
			更改密码
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.system.users.remove({user:"test"})
		</p>
		<p>
			或者db.removeUser('test')
		</p></td>
		<td>
		<p>
			删除用户
		</p></td>
	</tr>
</table>

<table>
	<tr>
		<td>
		<p>
		</p></td>
		<td>
		<p>
			use admin
		</p></td>
		<td>
		<p>
			超级用户
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.auth(‘test’, ‘test’)
		</p></td>
		<td>
		<p>
			用户授权
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;	&nbsp;	&nbsp;	&nbsp;	&nbsp;	&nbsp;	&nbsp;
		</p></td>
		<td>
		<p>
			db.system.users.find()
		</p></td>
		<td>
		<p>
			查看用户列表
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			show users
		</p></td>
		<td>
		<p>
			查看所有用户
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.printCollectionStats()
		</p></td>
		<td>
		<p>
			查看各collection的状态
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.printReplicationInfo()
		</p></td>
		<td>
		<p>
			查看主从复制状态
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			show profile
		</p></td>
		<td>
		<p>
			查看profiling
		</p></td>
	</tr>
	<tr>
		<td>
		<p>
			&nbsp;
		</p></td>
		<td>
		<p>
			db.copyDatabase('mail_addr','mail_addr_tmp')
		</p></td>
		<td>
		<p>
			拷贝数据库
		</p></td>
	</tr>
</table>

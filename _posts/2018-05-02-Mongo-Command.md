Sharing - Mongo Shell

```
https://www.youtube.com/watch?v=pWbMrx5rVBE

> cls // 清屏

> show dbs  // 显示所有db
admin   0.000GB
botdb   0.000GB
config  0.000GB
local   0.000GB

> use botdb  // 创建一个db，并切换到上面
switched to db botdb

> db  // 查看当前所在哪个db
botdb

> db.createUser({user:"brad",pwd:"1234",roles:["readWrite", "dbAdmin"]});   // 创建一个用户
Successfully added user: { "user" : "brad", "roles" : [ "readWrite", "dbAdmin" ] }

> db.getUsers()  // 查看当前所在db 的所有用户
[
	{
		"_id" : "test.brad",
		"user" : "brad",
		"db" : "test",
		"roles" : [
			{
				"role" : "readWrite",
				"db" : "test"
			},
			{
				"role" : "dbAdmin",
				"db" : "test"
			}
		]
	}
]

> db.dropUser("brad")
true

> db.createCollection("customers")  // 创建collection
{ "ok" : 1 }

> show collections  // 查看所有collection
customers

> db.customers.insert({first_name:"John", last_name:"Doe"});  // 插入
WriteResult({ "nInserted" : 1 })

> db.customers.getIndexes()

> db.customers.find()  // 查找一个document
{ "_id" : ObjectId("5abaef900089b6c92ad69de9"), "first_name" : "John", "last_name" : "Doe" }

> db.customers.insert([{first_name:"Stevent",last_name:"Smith"},{first_name:"Mary",last_name:"Mapple", gender:"female"}]);  // 可以直接插入多个列的值
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

> db.customers.find().pretty()  // help function
{
	"_id" : ObjectId("5abaef900089b6c92ad69de9"),
	"first_name" : "John",
	"last_name" : "Doe"
}
{
	"_id" : ObjectId("5abaf03f4475b1e20d2a3908"),
	"first_name" : "Stevent",
	"last_name" : "Smith"
}
{
	"_id" : ObjectId("5abaf03f4475b1e20d2a3909"),
	"first_name" : "Mary",
	"last_name" : "Mapple"
}

> db.customers.update({first_name:"John"},{first_name:"John",last_name:"Johnson"})  // 更新一个document，但是要全部信息再输入一遍，update 需要接收两个参数{},{}
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.customers.update({first_name:"John"},{$set:{last_name:"Joey"}})  // 不用把全部的重新输入一遍，直接更新
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.customers.update({first_name:"John"},{$set:{age:45}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })  // 也可以直接更新一个全新的属性

> db.customers.update({first_name:"John"},{$inc:{age:5}})  // 利用inc 来增加一个数字类型的值，5 是原数据上加5，John 的age 属性变为50
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 }) 

> db.customers.update({first_name:"John"},{$unset:{age:1}})  // 删除一个field，利用1 或者true 都可以
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.customers.update({first_name:"John"},{$unset:{age:true}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.customers.update({first_name:"NotInDBYet"},{first_name:"NotInDBYet",last_name:"Nlast_name"});  // 更新一个没在db 中的数据不会插入
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })

> db.customers.update({first_name:"NotInDBYet"},{first_name:"NotInDBYet",last_name:"Nlast_name"}, {upsert:true});  // 如果不存在就插入，利用upsert:true 为第三个参数
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("5abaf56cbfa11b5aec4839d0")
})

> db.customers.update({first_name:"John"},{$rename:{"gender":"sex"}});  // 重新定义一个field 的列，列名就会更改为sex
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.customers.remove({first_name:"NotInDBYet"});  // 删除数据
WriteResult({ "nRemoved" : 1 })

> db.customers.remove({first_name:"John"},{justOne:true});  // 利用justOne 来确保只删除一条数据
WriteResult({ "nRemoved" : 1 })

> db.customers.find({first_name:"Mary"}).pretty();  // 按照条件查找document
{
	"_id" : ObjectId("5abaf03f4475b1e20d2a3909"),
	"first_name" : "Mary",
	"last_name" : "Mapple"
}

> db.customers.find({$or:[{first_name:"Mary"},{first_name:"Stevent"}]});  // 按照`或` 的关系查找数据，$or 后面的必须是一个array，[]
{ "_id" : ObjectId("5abaf03f4475b1e20d2a3908"), "first_name" : "Stevent", "last_name" : "Smith" }
{ "_id" : ObjectId("5abaf03f4475b1e20d2a3909"), "first_name" : "Mary", "last_name" : "Mapple" }

> db.cv_users.count({"$and":[{"active_status":"RETURN"}, {"works_status":{$exists:false}}]});
700


// 重新命名
> db.cv_videos.find().pretty()
{
	"_id" : ObjectId("5aba009e3d3dbdbab6490917"),
	"video_id" : NumberLong(5),
	"video_created_at" : "2018-03-27 16:28:12",
	"video_digest_status" : 0,
	"video_activity_status" : 0,
	"user_id" : NumberLong(4),
	"category" : [
		1,
		2
	]
}
> db.cv_videos.update({"category":{$exists:true}}, {$rename:{"category":"categories"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.cv_videos.find().pretty()
{
	"_id" : ObjectId("5aba009e3d3dbdbab6490917"),
	"video_id" : NumberLong(5),
	"video_created_at" : "2018-03-27 16:28:12",
	"video_digest_status" : 0,
	"video_activity_status" : 0,
	"user_id" : NumberLong(4),
	"categories" : [
		1,
		2
	]
}


通过db.system.indexes.find()能够返回当前数据库中的所有索引



类型
> db.cv_videos.count({"categories":{$type:"string"}})
1292162
> db.cv_videos.count({"categories":{$type:2}})
1292162
> db.cv_videos.count({"categories":{$type:"int"}})
726942
> db.cv_videos.count()
3156051
> db.cv_videos.count({"categories":{$type:"undefined"}})
0



foreach
> db.cv_users.find().forEach( function(myDoc) { print( "user: " + myDoc.user_id ); } );
user: 4
user: 5
user: 000009
> db.cv_videos.find().forEach(function(data){print(data)});
[object BSON]
> db.cv_videos.find().forEach(function(data){print(data.categories)});
1,2
>


转换类型
db.cv_videos.find({"video_id":98350028}).pretty()
{
	"_id" : ObjectId("5ab9f4595878099d0853648d"),
	"user_id" : NumberLong(10784608),
	"video_id" : NumberLong(98350028),
	"video_activity_status" : 0,
	"video_digest_status" : 1,
	"video_created_at" : "2018-02-17 20:40:43",
	"categories" : 10030,
	"user_real_play" : NumberLong(607)
}
> db.cv_videos.find({"video_id":98350028}).forEach(function(data){db.cv_videos.update({_id:data._id},{$set:{"categories":[parseInt(data.categories)]}})})
> db.cv_videos.find({"video_id":98350028}).pretty()
{
	"_id" : ObjectId("5ab9f4595878099d0853648d"),
	"user_id" : NumberLong(10784608),
	"video_id" : NumberLong(98350028),
	"video_activity_status" : 0,
	"video_digest_status" : 1,
	"video_created_at" : "2018-02-17 20:40:43",
	"categories" : [
		10030
	],
	"user_real_play" : NumberLong(607)
}

> db.cv_videos.find({"categories":{$type:"string"}}).forEach(function(data){db.cv_videos.update({_id:data._id},{$set:{"categories":[parseInt(data.categories)]}})})
```

pymongo

```
client = MongoClient('mongodb://10.25.172.135:27017/')
db = client.botdb
cv_users = db.cv_users

cv_users.update_one({'user_id': uid}, {'$set': {'last_login_time': str(last_login_date) + " " + randomTime}})
cv_users.update_one({'user_id': uid}, {'$set': {'last_login_time': ts, 'active_status': 'ACTIVE'}})

for post in cv_videos.find({"video_created_at": {"$gt": "2018-01-31 23:59:59", "$lt": "2018-03-01 00:00:00"}}).sort("video_created_at"):


cv_videos.drop()


cv_videos.insert_one({'video_id': vid, 'user_id': uid, 'video_created_at': created_at,
                                  "video_digest_status": int(isPublished), "video_activity_status": int(isActivity)})
                                  


# coding:utf-8
# upsert 的用法
if __name__ == '__main__':
    # 用来更新用户状态的改变，只按照尾号进行记录
    from pymongo import MongoClient

    client = MongoClient('mongodb://127.0.0.1:27017/')
    db = client.botdb

    cv_user_states = db.cv_user_states
    cv_user_states.update({"from_active_status": 1}, {"$inc": {"count": 1}}, upsert=True)

```

mongo java

```

```


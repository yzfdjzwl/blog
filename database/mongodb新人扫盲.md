# 目录

* [前言](#前言)
* [使用bash操作mongodb](#使用bash操作mongodb)
    * [数据库基本命令](#数据库基本命令)
    * [增加数据](#增加数据)
    * [删除数据](#删除数据)
    * [更新数据](#更新数据)
        * [使用update()更新](#使用update更新)
        * [使用save()命令实现upsert](#使用save命令实现upsert)
        * [自动更新信息](#自动更新信息)
    * [查询数据](#查询数据)
    * [aggregate](#aggregate)
        * [$group](#group)
        * [$sum](#sum)
        * [$limit](#limit)
        * [$match](#match)
        * [$sort](#sort)
        * [$unwind](#unwind)
        * [$project](#project)
        * [$skip](#skip)
* [使用node操作mongodb](#使用node操作mongodb)
* [使用mongoose](#使用mongoose)


## 前言

1. [mongodb是什么?](https://www.mongodb.com/), 需fq
2. [如何安装mongodb?](http://www.runoob.com/mongodb/mongodb-window-install.html)

我将从2种语言来理解mongodb的基本操作, 分别是`javascript, bash`, 最后再介绍一下`mongoose`的常用的API。

## 使用bash操作mongodb

### 数据库基本命令

1. 显示所有数据库: `show dbs` 
2. 创建/使用数据库：`use dbName`
3. 创建数据库表，在mongoDB里又将表称为集合(collections), 因此创建表:`db.createCollection("collectionName")`
4. 显示数据库下的所有集合(表): `show tables / show collections`
5. 删除当前数据库: `db.dropDatabase()`
6. 查看当前所使用的数据库: `db.getName()`
7. 显示当前数据库的状态: `db.stats()` 
8. 显示当前数据库的版本: `db.version()`
9. 注意: `collection`和`table`概念差不多，`doc`和`row`概念差不多。

### 增加数据

1. 创建一个集合: `db.createCollection("tableName")`

2. 添加文档: 

```
db.tableName.insert({username: "john", age: "18"})

// 或者
db.tableName.save({username: "john", age: "18"});

// 它们之间的区别

// 1. 使用save，如果对象不存在则插入，如果存在，则会调用update方法。如果是insert, 或忽略调用update方法。 

// 2. insert可以插入一个列表，而不用遍历，效率高。而save需要遍历，效率不如insert。
```

### 删除数据

1. 删除操作: `db.tableName.remove({age: 18})`

### 更新数据

####  使用update()更新

```
db.tableName.update({age: 18}, {$inc: {age:10}}, false ,true)

// 等价于
update tableName set age = age + 10 where age = 18;

// 关于update
db.collection.update(
  <query>,
  <update>,
  {
    upsert: <boolean>,
    multi: <boolean>
  }
);

// {}里的是可选的
// upsert为true表示如果记录存在就更新，不存在就插入新的记录。
// multi为true表示更新所有匹配的文档，如果为false表示只更新第一个文档(默认行为)。
```

#### 使用save()命令实现upsert

```
// 如果不指定'_id'值，save()命令会认为它是一个插入操作。
// 如果指定，就是更新。

db.calendars.save({'uid': 'yuzf', 'projectId': 'test'}); // 插入一条数据
db.calendars.save({'_id': 'test', 'uid': 'yuzf', 'projectId': 'test'}); // 更新一条数据
```

#### 自动更新信息

```
// 将name为yuzf的人的成绩加4，如果存在就更新，不存在就创建。 
db.stu.update({'name': 'yuzf'}, {$inc: {grade: 4}}, {upsert: true});

// 设置字段值
db.stu.update({'name': 'yuzf'}, {$set: {'grade': 200}});

// 删除指定字段
db.stu.update({'name': 'yuzf'}, {$unset: {'grade': 200}});
```

### 查询数据

1. 获取指定名称的集合: `db.getCollection("tableName");`
2. 获取集合里所有的数据:`db.tableName.find().pretty();`
3. 获取集合里的指定数据:`db.users.find({'uid', 'yuzf'}).pretty();`
4. 获取集合里的指定数据，然后指向看指定数据, 在第二个参数里添加键，并设置键的值为1即可:`db.users.find({'uid': 'yuzf'}, {'uid': 1}).pretty();`
5. 使用函数sort, limit, skip
    * 以uid进行排序: `db.users.find().sort({ 'uid': 1 });`
    * 限制查询结果返回的最大数目为10: `db.users.find().limit(10);`
    * 返回查询结果除了前20条的文档的其他文档: `db.users.find().skip(20);`
6. 使用固定集合、自然顺序和$natural
    * 固定集合必须使用`createCollection()`, 以显示的方式创建: `db.createCollection("users", {capped: true, size: 20480})`
    * 因为固定集合的顺序和插入顺序是一样的, 如果想要逆转排序: `db.users.find().sort({ $natural: -1 }).limit(10);`
7. 获取单个文档: `db.users.findOne();`
8. 使用常用的聚合命令`count, distinct, group`
    * 统计users表里有多少个文档: `db.users.count();`
    * 去重: `db.users.distinct('uid');`, 将会返回一个数组，数组里包含的元素是去重了的`uid`。 
    * 将结果分组: 
```
  db.calendars.group({
    key: {uid: true},
    initial: {total: 0},
    reduce: function(items, prev) {
      prev.total += 1;
    }
  });

// 以uid进行分组。
// 为每个已分组的结果提供一个基数。
// 正在遍历的当前文档和聚集计数对象(我认为就是那个基数)。

// 最后的结果格式数据是:
[
  {
    'uid': 'yuzf',
    'total': 1,
  },
  {
    'uid': 'test',
    'total': 3,
  }
]
```
9. 使用条件操作符
    * 执行大于和小于($lt,$gt,$lte,$gte)比较: `db.users.find({ departmentNumber: {$lt: 100} });`
    * 获取除uid为yuzf以外的其他文档: `db.calendars.find({'uid': {$ne: 'yuzf'}}).pretty();`
    * 指定一个匹配的数组($in): `db.calendars.find({'uid': {$in: ['yuzf', 'yangh']}}).pretty();`
    * 查找某个不在数组中的值: `db.calendars.find({'uid': {$nin: ['yuzf', 'yangh']}}).pretty();`
    * 匹配文档中的所有属性($all): `db.calendars.find({'uid': {$all: ['yuzf', 'yangh']}}).pretty();`
    * 在文档中搜索多个表达式: `db.calendars.find({'uid': {$or: ['yuzf, 'yangh'']}}).pretty();`
    * 使用slice来获取文档(分页): `不知道为什么，自己跑的时候报错，$slice unknown operator`
    * 还有一些其他的，感觉不是很常用。

### aggregate

就我自己的理解，aggregate相当于Linux系统下的管道符，当前得到的结果可以作为下一次计算的输入。

有很多管道操作符，这里只是介绍一部分常用的操作符。

#### $group

关于group操作符, 它类似于关系数据库里的`group by`, 需要记住以下这几点:

1. 必须显示以`_id`作为分组。

2. 如果要以"什么"为分组，"什么"必须是对象。

3. `$`表示该元素是对文档中字段的引用。

看一个例子：

```
// 表结构是{device, isSmart, releaseTime, price}

db.phones.aggregate({$group: {_id: "$device"}, total: {$sum: "$price"}});

// 得到的结果是:
{"id": "iPhone5S", "total": 10000}
{"id": "iPhone6S", "total": 10200}
{"id": "iPhone6P", "total": 13000}
```

#### $sum

如果我们想要找到一个文档，计数就加一。那么我们可以使用`$sum`。就像上面的日子改写后就成了下面这样:

```
db.phones.aggregate({$group: {_id: "$device"}, count: {$sum: 1}});

// 得到的结果是:
{"id": "iPhone5S", "count": 18}
{"id": "iPhone6S", "count": 2}
{"id": "iPhone6P", "count": 10}
```

#### $limit

如果我们想要限制得到的数据只有10条，那么就会用到了`$limit`， 我认为它和查询时的`limit()`没有什么区别。

```
db.phones.aggregate({$group: {_id: "$device", count: {$sum: 1}}}, {$limit: 10});
```

#### $match

我猜想这个管道符应该用得特别多，通过它可在聚集管道中高效地返回一个普通地MongoDB查询的结果。

`$match`操作符，最好位于管道的开始, 因为这样会大大减少开销。

```
db.phones.aggregate({$match: {"device": "iPhone9S"}});
```

#### $sort

它和普通查询里的`sort`没有什么不同。

#### $unwind

这应该也是一个特别常用的管道符, 它接受一个数组并将每个元素分割到一个新的文档中（在内存中而不是添加到集合中）。

#### $project

如果我们查询出来我们想要的数据，但是只是想要一部分的字段，该如何呢？那么就会用到`$project`这个管道符了。

```
// 只会显示_id和device这两个字段
db.phones.aggregate({$project: {_id: 1, device: 1}});
```

#### $skip

同样，它和普通查询里的`skip`没有什么不同。如果我们想要得到3000条数据里的第1500~1510条数据该怎么写呢?

```
db.phones.aggregate({$skip: 1500, $limit: 10});
```

## 使用node操作mongodb

### 连接到mongo

### 

## 使用mongoose

未完待续...

**参考:**

[mongoDB基本使用](http://www.cnblogs.com/TankMa/archive/2011/06/08/2074947.html)
[mongodb大数据处理权威指南](http://download.csdn.net/detail/qinghuawenkang/8196313)

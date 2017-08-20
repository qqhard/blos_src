title: pymongo操作散记
date: 2014-02-28 18:39:09
tags:
- pymongo
- python
- mongodb
---

### 链接数据库
使用`MongoClient`这个类，原有的`Connection()`已经不推荐使用了.

```
from pymongo import MongoClient
client = MongoClient()
```

<!-- more -->

### 创建数据库和表
传统关系型数据库table和record对应着mongdb的collection和document。

db和collection是不用预先创建的，插入一条数据时是必须要指明db和collection的，这个时候就建立了，牛逼的是同一个collection的字段可以不一样。

可以用属性链的形式：
```
table = client.dbname.tablename
table.insert_one({'name':'hello'})
table.insert_one({'age':'10'})
```
这样插入了两个document，字段完全不同都没关系

也可以用字典的形式:
```
db = client['dbname']
table = db['tablename']
```

### 更新时如果没有就插入

`update_one`和`update_many`有第3个参数`upsert`,可以为True或False，默认为False。为False时没找到就不更新，为True时没找到就插入.
```python
m = {'name':'hello','age':10}
client.db.table.update_one({'name':'hello'},{'$set':m},True)
```

# 视图（Views）

从3.4版本开始，MongoDB支持从集合或者其他视图中创建只读视图。

### 创建视图

创建或定义一个视图，MongoDB 3.4引进：

create命令有viewOn和pipeline选项（ [`db.createCollection`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) 方法中也有）：

```text
db.runCommand( { create: <view>, viewOn: <source>, pipeline: <pipeline> } )
```

或者为视图指定一个默认的 [collation](https://docs.mongodb.com/manual/release-notes/3.4/#relnotes-collation) ：

```text
db.runCommand( { create: <view>, viewOn: <source>, pipeline: <pipeline>, collation: <collation> } )
```

新版本的Mongo Shell帮助程序 [`db.createView()`](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView)：

```text
db.createView(<view>, <source>, <pipeline>, <collation> )
```

### 行为

视图展示了以下的行为：

#### 只读

视图是只读的，对视图进行写操作会出现错误。

视图支持的读操作如下：

* [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)
* [`db.collection.findOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#db.collection.findOne)
* [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)
* [`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count)
* [`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct)

#### 使用索引和排序操作

* 视图使用基础集合（`创建时的<source>`）的索引
* 如果索引属于基础集合，不能在视图上直接创建，删除，重建索引，也不能在视图上获取索引列表。
* 不能在视图上指定 [`$natural`](https://docs.mongodb.com/manual/reference/operator/meta/natural/#metaOp._S_natural) 排序。下面的操作是无效的：

  ```text
  db.view.find().sort({$natural: 1})
  ```

#### 投影限制（Projection Restrictions）

视图中 [`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) 操作不支持以下的[投影](https://docs.mongodb.com/manual/reference/operator/projection/)操作：

* [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_)
* [`$elemMatch`](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch/#proj._S_elemMatch)
* [`$slice`](https://docs.mongodb.com/manual/reference/operator/projection/slice/#proj._S_slice)
* [`$meta`](https://docs.mongodb.com/manual/reference/operator/projection/meta/#proj._S_meta)

#### 不变的名字

不能重命名视图。

#### 视图的创建

* 视图在有读操作时按需计算，并且MongoDB在视图上执行读操作是作为底层聚合管道的一部分。因此，视图不支持如下操作：

  * [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce),
  *  [`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text) 操作符，因为$text操作符尽在聚合的第一阶段有效，
  *  [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear) 命令和 [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear) 管道阶断

* 如果聚合管道在创建时禁止使用\_id_字段，_此视图中的文档不会有\_id字段。

#### 分片视图

如果基础集合是分片的，视图也会是分片的。因此，分片视图不支持在字段中使用  [`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup)` 和` [`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup)` `操作符。

#### 视图和整理（Views and Collation）

* 可以在创建视图的时候指定一个默认的整理（collation）。如果没有整理被指定，视图默认的整理是“简单”的二元对比整理器。意思是，视图不会继承基础集合的默认整理。
* 视图的字符串比较使用默认的整理。尝试修改或者复写视图默认整理的操作会失败并返回错误。
* 如果创建一个新视图是基于另外一个视图，不能指定和基础视图不同的整理。
* 如果执行一个包含很多视图的聚合，如带有 [`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup)` `或者  [`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup)，这些视图必须是同样的整理。

#### 公共视图定义

列出集合的操作，例如 [`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos)  和  [`db.getCollectionNames()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionNames/#db.getCollectionNames)` `，输出中包含视图的信息。

{% hint style="warning" %}
重要：

视图定义是公开的；即对视图使用 [`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos) 和explain操作将会包含定义视图的管道。因此，在视图定义中避免直接涉及敏感的字段和值。
{% endhint %}

#### 删除视图

要移除视图，在视图上使用 [`db.collection.drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop)  方法。

#### 修改视图

修改视图可以通过删除并重建视图的方式或者使用 [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod)  命令。

#### 支持的操作

以下操作提供对视图的支持，除了本页中提到的限制：



| Commands | Methods |
| --- |


| [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)[`db.createView()`](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView) |
| --- |


| [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod) |   |
| --- |


|   | [`db.getCollection()`](https://docs.mongodb.com/manual/reference/method/db.getCollection/#db.getCollection)[`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos)[`db.getCollectionNames()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionNames/#db.getCollectionNames) |
| --- |


| [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)[`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct)[`count`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count) | [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)[`db.collection.findOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#db.collection.findOne)[`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count)[`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct) |
| --- |





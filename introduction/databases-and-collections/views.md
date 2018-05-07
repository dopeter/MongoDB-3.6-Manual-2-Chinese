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

* 视图在有读操作时按需计算，


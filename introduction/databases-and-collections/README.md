# 数据库和集合（Databases and Collections）

MongoDB存储[BSON文档](https://docs.mongodb.com/manual/core/document/#bson-document-format)，即数据记录，在[集合](https://docs.mongodb.com/manual/reference/glossary/#term-collection)中，集合在数据库中。（数据库包含集合，集合包含BSON文档数据记录）。

![](../../.gitbook/assets/image%20%281%29.png)

### 数据库

在MongoDB中，数据库保存文档的集合（collections of documents）。

要选择一个数据库来使用，在Mongo Shell中，使用 use &lt;db&gt; 语句，示例如下：

```text
use myDB
```



#### 创建一个数据库

如果数据库不存在，MongoDB会在第一次向这个数据库中存储数据时创建它。因此，你可以切换至一个不存在的数据库并在Mongo Shell中执行下面的操作：

```text
use myNewDB

db.myNewCollection1.insertOne( { x: 1 } )
```

如果数据库myNewDB和集合myNewCollection1不存在， [`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) 操作会创建它们。

关于数据库名称的限制，参见[命名限制](https://docs.mongodb.com/manual/reference/limits/#restrictions-on-db-names) 。



### 集合

如果一个集合不存在，MongoDB会在第一次存储数据时创建它。

```text
db.myNewCollection2.insertOne( { x: 1 } )
db.myNewCollection3.createIndex( { y: 1 } )
```

 如果[`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) 和 [`createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) `操作的集合不存在，它们会各自被创建。`

关于集合名称的限制，参见[命名限制](https://docs.mongodb.com/manual/reference/limits/#restrictions-on-collection-names)。

#### 显式创建（Explicit Creation）

MongoDB提供了 [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) 方法显式创建一个集合以及各种选项，例如设置最大容量或者文档的验证规则。如果没有特别指定这些选项，可以不用显式创建集合，因为MongoDB会在第一次存储数据时自动创建它。

关于修改集合选项，参见  [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod)`。`

#### 文档验证（Document Validation）

3.2版本提供的新特性。

默认情况下，一个集合不需要它包含的文档都有同样的模式（schema）；意指一个集合中的文档不需要拥有相同的字段集（set of fields），并且集合内文档的字段的数据类型都可以不同。

在3.2版本开始，可以在集合的更新和新增操作的时候强制执行[文档验证规则](https://docs.mongodb.com/manual/core/schema-validation/) 。详细请参见[模式验证](https://docs.mongodb.com/manual/core/schema-validation/)。

#### 修改文档结构

要改变一个集合中文档的结构，例如新增字段，移除已有的字段，或者修改字段指更改为新类型，请将文档更新为新的结构。

#### 唯一标识符（Unique Identifiers）

3.6版本新特性。

{% hint style="success" %}
重要：

featureCompatibilityVersion必须设置为“3.6”，更多内容见 [View FeatureCompatibilityVersion](https://docs.mongodb.com/manual/reference/command/setFeatureCompatibilityVersion/#view-fcv)。
{% endhint %}

集合会被分配一个不变的UUID。集合的UUID在复制集和分片中不变。

查询集合的UUID，可是使用  [listCollections](https://docs.mongodb.com/manual/reference/command/listCollections)命令或者 [`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos) `方法。`


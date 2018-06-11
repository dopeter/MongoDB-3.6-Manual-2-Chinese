# MongoDB CRUD 操作

CRUD操作包括对文档的增、删、改、查。

### 创建操作（Create Operations）

创建或插入操作是指在一个集合中新增文档。如果当前不存在该集合，插入操作会自动创建该集合。

MongoDB支持如下的方法来插入文档至集合：

* [db.collection.insertOne\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) _New in version 3.2_
* [db.collection.insertMany\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany) _New in version 3.2_

在MongoDB中，插入操作的目标是单独的集合。MongoDB中所有的写操作在文档级别上是原子的。

![](../.gitbook/assets/image%20%283%29.png)

示例请参见[插入文档](https://docs.mongodb.com/manual/tutorial/insert-documents/) 。

### 读操作（Read Operations）

读操作从集合中检索文档，相当于在一个集合中查询文档。MongoDB提供如下的方法在一个集合中读取文档：

* [db.collection.find\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)

你可以选择[过滤或者条件](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-query-argument)来指定返回的文档

![](../.gitbook/assets/image%20%284%29.png)

示例请参见：

* [查询文档](https://docs.mongodb.com/manual/tutorial/query-documents/)
* [查询嵌套文档](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [查询数组](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [查询嵌套数组](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

### 更新操作（Update Operators）

更新操作修改在一个集合中的文档。MongoDB提高如下的方法在一个集合中更新文档

* [db.collection.updateOne\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne) _New in version 3.2_
* [db.collection.updateMany\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany) _New in version 3.2_
* [db.collection.replaceOne\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne) _New in version 3.2_

更新操作是针对单个集合的。MongoDB中所有的写操作在文档级别上是原子的。

你可以指定条件或者过滤来确定要更新的文档。过滤器使用的语法和读操作一样。

![](../.gitbook/assets/image%20%285%29.png)

示例请参见[更新文档](https://docs.mongodb.com/manual/tutorial/update-documents/)。

### 删除操作（Delete Operators）

删除操作从一个集合中删除文档。MongoDB提供如下方法在一个集合中删除文档。

* [~~db.collection.deleteOne\(\)~~](https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne) _New in version 3.2_
* [db.collection.deleteMany\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany) _New in version 3.2_

删除操作是针对单个集合的。MongoDB中所有的写操作在文档级别上是原子的。

你可以指定条件或者过滤来确定要删除的文档。过滤器使用的语法和读操作一样。

更多示例参见[删除文档](https://docs.mongodb.com/manual/tutorial/remove-documents/)。

### 块级写入（Bulk Write）

MongoDB提供了大块级写入的能力。详情请阅览 [Bulk Write Operations](https://docs.mongodb.com/manual/core/bulk-write-operations/) 。


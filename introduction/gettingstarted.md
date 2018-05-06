# 入门指南

{% tabs %}
{% tab title="Mongo 脚本（Shell）" %}
接下来的教程使用Mongo脚本执行数据插入和数据查询操作。

### 预备知识

此教程需要你连接到以下任一项：

*  **MongoDB Atlas Free Tier Cluster（MongoDB提供的云服务的免费集群）** MongoDB Atlas是一个快速、简单和免费学习MongoDB的途径。跟随[创建集群](https://docs.mongodb.com/manual/tutorial/atlas-free-tier-setup/#create-free-tier-manual)教程来开始 MongoDB Atlas。
* 本地MongoDB安装。更多关于本地安装MongoDB的信息，参见[安装MongoDB](https://docs.mongodb.com/manual/installation/#tutorial-installation)

### 新增文档

 [`db.collection.insertMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany) 能新增多个[文档](https://docs.mongodb.com/manual/core/document/)至一个[集合（Collection）](https://docs.mongodb.com/manual/core/databases-and-collections/)

 。传递一个文档数组给该方法。

```javascript
db.inventory.insertMany([
   // MongoDB adds the _id field with an ObjectId if _id is not present
   { item: "journal", qty: 25, status: "A",
       size: { h: 14, w: 21, uom: "cm" }, tags: [ "blank", "red" ] },
   { item: "notebook", qty: 50, status: "A",
       size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank" ] },
   { item: "paper", qty: 100, status: "D",
       size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank", "plain" ] },
   { item: "planner", qty: 75, status: "D",
       size: { h: 22.85, w: 30, uom: "cm" }, tags: [ "blank", "red" ] },
   { item: "postcard", qty: 45, status: "A",
       size: { h: 10, w: 15.25, uom: "cm" }, tags: [ "blue" ] }
]);
```

 [`insertMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany)`方法返回一个文档包含新插入文档的_id字段值。示例参见`[引用文档](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#insertmany-examples) 。

使用  [`db.collection.insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)` 新增一个单独的文档。`

更多的内容和示例，参见[新增文档](https://docs.mongodb.com/manual/tutorial/insert-documents/#write-op-insert)在[CRUD](https://docs.mongodb.com/manual/crud/#crud)部分。

### 查询文档

#### 查询所有文档

在一个集合中查询所有文档，传递一个空文档作为[查询过滤文档](https://docs.mongodb.com/manual/core/document/#document-query-filter)（query filter document）至 [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)` 方法：`



```text
db.inventory.find( {} )
```

查询一个匹配指定查询条件的文档，传递给 [`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)  方法一个使用键值对（&lt;field&gt;:&lt;value&gt;）的所需文档作为查询过滤文档。下面的示例查询inventory集合中匹配status是“D”的所有文档：



```text
db.inventory.find( { status: "D" } )
```

#### 匹配嵌入的文档

要匹配嵌入的文档需要准确的匹配指定的value文档，包括字段顺序。下面的示例查询了字段size等于文档 { h: 14, w: 21, uom: "cm" }：



```text
db.inventory.find( { size: { h: 14, w: 21, uom: "cm" } } )
```

#### 匹配嵌入文档中的一个字段

接下来的示例演示了查询size字段的内嵌字段uom等于字符串“in”的文档：



```text
db.inventory.find( { "size.uom": "in" } )
```

#### 匹配数组中的元素

下面的示例演示查询一个数组字段tags中包含字符串“red”的一个元素的文档：



```text
db.inventory.find( { tags: "red" } )
```

#### 完全匹配一个数组

下面的示例演示查询数组字段tags中的两个元素，“red”和“blank”，并且使用指定的顺序：



```text
db.inventory.find( { tags: ["red", "blank"] } )
```

更多内容和示例，参见[查询文档](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-queries)和[CRUD](https://docs.mongodb.com/manual/crud/#crud)部分。

更新和删除文档，参见[更新文档](https://docs.mongodb.com/manual/crud/#crud)和[删除文档](https://docs.mongodb.com/manual/tutorial/remove-documents/#write-op-delete)。

### 下一步

一旦 你完成了入门指南，你可以在下面找到有用的课程和主题：

学习更多的MongoDB查询语言和其他MongoDB基本原理，请在 [M001: MongoDB Basics](https://university.mongodb.com/courses/M001/about?_ga=2.44420260.12181035.1525329637-358776125.1525329637) 注册。

|  Introduction |  Developers |  Administrators |  Reference |  |
| --- | --- | --- | --- | --- |
|  [Introduction to MongoDB](https://docs.mongodb.com/manual/introduction/) |  [CRUD Operations](https://docs.mongodb.com/manual/crud/) |  [Production Notes](https://docs.mongodb.com/manual/administration/production-notes/) |  [Shell Methods](https://docs.mongodb.com/manual/reference/method/) |  |
|  [Installation Guides](https://docs.mongodb.com/manual/installation/) |  [Aggregation](https://docs.mongodb.com/manual/aggregation/) |  [Replica Sets](https://docs.mongodb.com/manual/replication/) |  [Query Operators](https://docs.mongodb.com/manual/reference/operator/) |  |
|  [Databases and Collections](https://docs.mongodb.com/manual/core/databases-and-collections/) |  [SQL to MongoDB](https://docs.mongodb.com/manual/reference/sql-comparison/) |  [Sharded Clusters](https://docs.mongodb.com/manual/sharding/) |  [Reference](https://docs.mongodb.com/manual/reference/) |  |
|  [Documents](https://docs.mongodb.com/manual/core/document/) |  [Indexes](https://docs.mongodb.com/manual/indexes/) |  [MongoDB Security](https://docs.mongodb.com/manual/security/) |  [Glossary](https://docs.mongodb.com/manual/reference/glossary/) |  |
{% endtab %}

{% tab title="Python" %}
和脚本一样的内容，只是示例代码不同。
{% endtab %}

{% tab title="Node.js" %}
 和脚本一样的内容，只是示例代码不同。
{% endtab %}

{% tab title="Compass" %}

{% endtab %}
{% endtabs %}


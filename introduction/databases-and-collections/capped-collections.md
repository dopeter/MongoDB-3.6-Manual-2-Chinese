# 定容集合（Capped Collections）

### 概览

[定容集合](https://docs.mongodb.com/manual/reference/glossary/#term-capped-collection) （固定大小的集合，在达到最大容量时覆写旧的记录。）是固定大小的集合，支持基于插入顺序的插入和检索文档的高吞吐量操作。定容集合和循环缓冲的工作方式类似，一旦集合填满了它被分配的空间，它会通过覆写旧文档来作为新文档的空间。

更多内容参见  [`createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)  或者  [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)  。

### 行为

#### 插入顺序

定容集合保证插入的顺序。结果是，查询不需要索引来按照插入顺序返回文档。去除了顺序索引的限制，定容集合可以支撑更高的吞吐量。`（其实就是顺序写）`

#### 自动移除旧文档

为了给新文档腾出空间，定容集合会自动移除集合中最旧的文档，不需要命令脚本或者显式移除操作。

例如， [oplog.rs](https://docs.mongodb.com/manual/reference/glossary/#term-oplog) 使用定容集合来存储复制集的操作日志。

考虑以下可能使用定容集合的用例：

* 存储大容量系统生成的日志信息。使用去除顺序索引的定容集合插入文档的速度非常接近直接将日志信息写入文件系统。此外，内置的先进先出属性管理着事件的顺序，可以在管理存储的时候使用。
* 在定容集合中缓存小量的数据。因为缓存的读比写的比重要高很多，你可能需要确保集合始终在工作区中（即在内存中），或者接受在写操作时必要的索引或者多个索引的写损失。

#### id 索引（\_id Index）

定容集合默认生成id字段以及一个\_id字段的索引。



### 限制和建议

#### 更新

如果你计划在定容集合中更新文档，创建一个索引以便更新操作不需要集合扫描（collection scan，遍历集合）。

#### 文档大小

在3.2版本中改变。

如果一个更新或者替换操作改变了文档的大小，这个操作会失败。

#### 文档删除

不能在定容集合中删除文档。要删除集合中所有的文档，使用 [`drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop) 方法删除集合并重建定容集合。

#### 分片

不能对定容集合分片。

#### 查询效率

在集合中使用自然排序检索最近插入的元素是高效的。这（有点）像查看日志文件的尾巴。（This is analogous to tail on a log file.）

#### 聚合 $out

不能将聚合管道操作符 [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out) 的结果写入定容集合。



### 步骤（Procedures）

#### 创建一个定容集合

创建定容集合必须显式的使用  [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)` `方法，是Mongo Shell中创建命令的助手。创建定容集合时必须指定集合的容量计量单位是字节，MongoDB会为集合预分配。定容集合的空间中会有一小部分储存内部的头信息。

```text
db.createCollection( "log", { capped: true, size: 100000 } )
```

如果size字段小于等于4096，集合的容量会被设置为4096。否则MongoDB会提高容量使其是256的整数倍。

此外，也可以指定集合中文档的最大数量，使用max字段，示例如下：

```text
db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )
```

{% hint style="warning" %}
重要：

size参数一直都是必须的，即使指定了文档的最大数量。如果集合达到了最大容量限制MongoDB会移除旧文档，即使未达到最大文档数也会如此。`（所以优先级是 size>max ，达到谁的limit都会移除旧文档）`
{% endhint %}

参见：

 [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) 和 [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)。

#### 查询定容集合

如果对定容集合执行 [`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) 并且没有指定顺序，MongoDB保证返回结果的顺序和插入的顺序一致。将

 [`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)连同 [`sort()`](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)  方法以及 [`$natural`](https://docs.mongodb.com/manual/reference/operator/meta/natural/#metaOp._S_natural)  参数设为-3，如下所示：

```text
db.cappedCollection.find().sort( { $natural: -1 } )
```

#### 检查一个集合是否是定容

使用 [`isCapped()`](https://docs.mongodb.com/manual/reference/method/db.collection.isCapped/#db.collection.isCapped)  方法判定一个集合是否是定容，如下：

```text
db.collection.isCapped()
```

#### 转换一个集合至定容集合

使用 [`convertToCapped`](https://docs.mongodb.com/manual/reference/command/convertToCapped/#dbcmd.convertToCapped)  命令可以将一个非定容集合转换为定容集合：

```text
db.runCommand({"convertToCapped": "mycoll", size: 100000});
```

size参数指定了定容集合的大小以字节为单位。

在操作整个执行期间会获取数据库的排他锁。其他获取同样数据库锁的操作会被阻塞直到整个操作完成。

参见  [What locks are taken by some common client operations?](https://docs.mongodb.com/manual/faq/concurrency/#faq-concurrency-operations-locks) 解释了操作会使用的数据库锁。



#### 经过指定的时间周期后自动移除数据

作为定容集合的另外一种替代，可以考虑使用MongoDB的 [TTL](https://docs.mongodb.com/manual/reference/glossary/#term-ttl) （“存活时间”）索引。在  [Expire Data from Collections by Setting TTL](https://docs.mongodb.com/manual/tutorial/expire-data/) （通过设置TTL使集合中的数据过期）中有具体描述。这些索引可以移除或者标记过期集合中基于数据类型字段的值和索引的TTL值的数据。

{% hint style="warning" %}
重要：

 [TTL indexes](https://docs.mongodb.com/manual/tutorial/expire-data/) 与定容集合不兼容。
{% endhint %}

#### 尾部游标（Tailable Cursor，字典翻译是可追踪的游标）

可以在定容集合中使用[尾部游标](https://docs.mongodb.com/manual/reference/glossary/#term-tailable-cursor) 。类似于Unix的tail -f命令，尾部游标指向定容集合的尾部。当新的文档插入定容集合，可以使用尾部游标继续检索文档。

在创建尾部游标时查看 [Tailable Cursors](https://docs.mongodb.com/manual/core/tailable-cursors/)  以获取更多内容。


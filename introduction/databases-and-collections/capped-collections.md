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


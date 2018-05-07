# 定容集合（Capped Collections）

### 概览

[定容集合](https://docs.mongodb.com/manual/reference/glossary/#term-capped-collection) （固定大小的集合，在达到最大容量时覆写旧的记录。）是固定大小的集合，支持基于插入顺序的插入和检索文档的高吞吐量操作。定容集合和循环缓冲的工作方式类似，一旦集合填满了它被分配的空间，它会通过覆写旧文档来作为新文档的空间。

更多内容参见  [`createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)  或者  [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)  。

### 行为


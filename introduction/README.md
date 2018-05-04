# MongoDB简介

MongoDB是一个开源的文档数据库，提供了高性能、高可用和自动缩放的特性。

### 文档数据库

一条记录在MongoDB中是一个文档，由多个键值对组成。MongoDB文档类似于JSON对象。值（value of field）可以包含其他文档、数组或者文档集合（arrays of documents）。

![](../.gitbook/assets/image.png)

使用文档的优势：

* 文档和很多编程语言的原生数据类型相似
* 内嵌的文档和数组减少了昂贵的连接查询`（不过有些设计师仍会使用MongoDB的连接查询，并且MongoDB自己也为文档间的连接查询做了设计）`
* 文档动态的架构支持流畅的多态`（翻译的好生硬，Dynamic schema supports fluent polymorphism.想存什么字段就存什么字段，别想多了，肯定有限制）`

### 关键特性


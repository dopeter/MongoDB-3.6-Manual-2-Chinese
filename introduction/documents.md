# 文档（Documents）

MongoDB将数据记录存储位BSON文档。BSON是JSON文档的二进制表示形式，但它比JSON包含更多的数据类型。BSON的说明书，参见  [bsonspec.org](http://bsonspec.org/) ，另请参阅   [BSON Types](https://docs.mongodb.com/manual/reference/bson-types/)。

![](../.gitbook/assets/image%20%282%29.png)

### 文档结构

MongoDB文档是由键值对组成的并具有如下的结构：

```text
{
   field1: value1,
   field2: value2,
   field3: value3,
   ...
   fieldN: valueN
}
```

字段值可以是BSON支持的[数据类型](https://docs.mongodb.com/manual/reference/bson-types/)，也可以包含其他的文档，数组，以及文档数组。

例如下面的文档包含不同类型的值：

```text
var mydoc = {
               _id: ObjectId("5099803df3f4948bd2f98391"),
               name: { first: "Alan", last: "Turing" },
               birth: new Date('Jun 23, 1912'),
               death: new Date('Jun 07, 1954'),
               contribs: [ "Turing machine", "Turing test", "Turingery" ],
               views : NumberLong(1250000)
            }
```

上面的字段包含如下的数据类型：

* \_id 是一个 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)。
* name是一个内嵌文档包含first和last字段。
* birth和death是日期类型。
* contribs是字符串数组。
* views是长整型。

#### 字段名称（Field Names）

字段名称是字符串。

文档对字段名称有如下的限制：

* 字段\_id是作为主键保留的，它的值一定是集合中唯一的，不可变的，可以是除了数组之外的任何类型。
* 不能以$符号开始。
* 不能包含dot（.）字符
* 不能包含空字符

BSON文档支持多个字段有同样的名称。但大多数MongoDB接口使用不支持重复字段名（例如哈希表）的结构来表示MongoDB。如果你需要修改文档让其支持多个字段有相同名称，参阅驱动程序（driver，实际是不同语言的客户端）的 [driver documentation](https://docs.mongodb.com/manual/applications/drivers/) 。

一些MongoDB内部进程创建的文档也许会包含重复名称的字段，但MongoDB进程不会将重复名称的字段添加至用户已有的文档中。

#### 字段值限制

对于被索引的集合，索引的字段值有  [`Maximum Index Key Length`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit)` `限制（1MB的限值）。具体请参阅 [`Maximum Index Key Length`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit)  。

### 点标记法（Dot Notation）

MongoDB使用点标记法访问数组元素和内嵌的文档的字段。

#### 数组

指定或访问数组的一个元素使用0开始的索引位置，连接数组名称和dot（.）以及0开始的索引位置，括在引号中：

```text
"<array>.<index>"
```

例如，一个文档中有如下的字段：

```text
{
   ...
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   ...
}
```

指定contribs数组中第三个元素，使用点标记法 "contribs.2"。

查询数组的更多示例，参阅：

* 查询数组 [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* 内嵌的文档中查询数组 [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

另请参阅：

*  [`$[]`](https://docs.mongodb.com/manual/reference/operator/update/positional-all/#up._S_[])所有位置操作符用于更新操作
*  $\[/&lt;identifier/&gt;\] 过滤位置操作符用于更新操作
*  [`$`](https://docs.mongodb.com/manual/reference/operator/update/positional/#up._S_) 位置操作符用于更新操作
*  [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_) 当数组位置索引未知时的投影操作符
* 使用点标记法[查询数组](https://docs.mongodb.com/manual/tutorial/query-arrays/#read-operations-arrays)的示例

#### 内嵌文档

指定或访问内嵌文档的字段使用点标记法，连接内嵌文档名称以及dot（.）和字段名，用引号

```text
"<embedded document>.<field>"
```

例如，一个文档有如下的字段：

```text
{
   ...
   name: { first: "Alan", last: "Turing" },
   contact: { phone: { type: "cell", number: "111-222-3333" } },
   ...
}
```




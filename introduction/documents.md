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

对于被索引的集合，索引的字段值有  [`Maximum Index Key Length`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit) ``限制（1MB的限值）。具体请参阅 [`Maximum Index Key Length`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit)  。

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

* 要指定name字段中内嵌的last字段，使用点标记法“name.last”。
* 要指定contract字段中的phone字段中的number字段，使用点标记法“contact.phone.number”。

更多内嵌文档的查询示例，参阅：

* [Query on Embedded/Nested Documents](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

### 文档限制

文档具有如下属性：

#### 文档大小限制

BSON文档的最大值是16MB。

文档的最大值确保不会使用过多的RAM，在传输过程中不占用过多的带宽。要存储超过大小限制的文档，MongoDB提供了GridFS API，参阅 [`mongofiles`](https://docs.mongodb.com/manual/reference/program/mongofiles/#bin.mongofiles)  和[客户端驱动](https://docs.mongodb.com/manual/applications/drivers/) 文档获取关于GridFS的更多内容。

#### 文档字段顺序

MongoDB按照写操作的顺序保留文档字段的顺序，以下情况除外：

* \_id字段永远是第一个字段
* [重命名](https://docs.mongodb.com/manual/reference/operator/update/rename/#up._S_rename) 字段的更新操作可能会导致文档字段的重排序

在2.6版本中的修改：从2.6版本开始，MongoDB主动尝试保存文档中的字段顺序。在2.6版本以前，MongoDB不会主动保存文档的字段顺序。

#### \_id字段

在MongoDB中，每个集合中的文档都需要一个唯一的[\_id](https://docs.mongodb.com/manual/reference/glossary/#term-id) 字段来作为[主键](https://docs.mongodb.com/manual/reference/glossary/#term-primary-key) 。如果一个插入文档的操作忽略了_id字段，MongoDB会自动为\_id字段_生成一个 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid) 。

也适用于使用 [upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#upsert-parameter) 通过更新操作来插入文档。

\_id字段具有如下行为和约束：

* 默认的，MongoDB在创建集合期间会为\_id字段生成一个唯一索引。
* 文档中id字段永远是第一个字段。如果服务器接收到一个未包含\_id字段的文档，服务器会将\_id字段移到开头。
* \_id字段可以包含任意的[BSON数据类型](https://docs.mongodb.com/manual/reference/bson-types/)，除了数组。

{% hint style="danger" %}
警告：

为确保功能的复制，不要存储BSON的正则表达式在\_id字段中。`（正则表达式可能会覆盖新增的功能或者修改后的功能的主键id）`
{% endhint %}

以下是\_id存储值的常用选项：

* 使用 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)。
* 使用原生唯一标识符，如果启用，会节省空间并避免额外的索引。
* 生成自增长数字。
* 在应用代码里生成唯一标识符（UUID）。在集合和\_id索引中更高效的存储UUID的方式推荐使用BSON BinData 数据类型。 BinData的索引键（index keys）如果符合以下标准会更高效的存储：
  * 二进制子类型值为0-7或者128-135，并且
  * 二进制数组的长度为： 0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 12, 14, 16, 20, 24, 或者32。
* 使用驱动的BSON UUID生成组件来生成UUID。需要注意的是驱动实现的UUID在序列化和反序列化会有不同的逻辑，它可能与其他的驱动不兼容。更多请参阅  [driver documentation](https://api.mongodb.com/?_ga=2.108099906.1737614590.1528102232-358776125.1525329637)  关于UUID的互通性。

{% hint style="success" %}
注意

大多数MongoDB驱动客户端会在发送插入操作前包含\_id字段以及生成ObjectId；然而，如果客户端发送一个文档没有包含\_id字段， [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 会添加\_id字段并且生成ObjectId。
{% endhint %}

### 文档结构的其他用途

除了定义数据记录，MongoDB始终使用文档结构，包含但不限于：[查询过滤](https://docs.mongodb.com/manual/core/document/#document-query-filter) ，[更新文档规格](https://docs.mongodb.com/manual/core/document/#document-update-specification) ， [索引文档规格](https://docs.mongodb.com/manual/core/document/#document-index-specification) 。

#### 查询过滤文档

查询过滤文档指定了条件来决定那些记录被查询来进行读取、更新、删除操作。

你可以使用 &lt;field&gt;:&lt;value&gt;表达式来指定相等的条件以及[查询操作符](https://docs.mongodb.com/manual/reference/operator/query/) 表达式。

```text
{  <field1>: <value1>,  <field2>: { <operator>: <value> },  ...}
```

示例请参见：

* [查询文档](https://docs.mongodb.com/manual/tutorial/query-documents/)
* [查询嵌入式/嵌套文档](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [查询一个数组](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [查询在嵌入式文档中的数组](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

#### 更新文档规格

更新文档规格使用[update操作符](https://docs.mongodb.com/manual/reference/operator/update/#id1) 在 [db.collection.update\(\)](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update) 执行期间在指定字段上的数据修改。

```text
{  <operator1>: { <field1>: <value1>, ... },  <operator2>: { <field2>: <value2>, ... },  ...}
```

更多示例，参见 [Update specifications](https://docs.mongodb.com/manual/tutorial/update-documents/#update-documents-modifiers)。

#### 索引文档规格

索引文档规格定义了要索引的字段以及索引的数据类型：

```text
{ <field1>: <type1>, <field2>: <type2>, ...  }
```

### 其他资源

[关于文档的思考Part1](https://www.mongodb.com/blog/post/thinking-documents-part-1?jmp=docs&_ga=2.101276609.1737614590.1528102232-358776125.1525329637)


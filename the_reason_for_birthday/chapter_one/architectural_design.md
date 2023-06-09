# 架构设计

## 映射单元

column和field是数据最基本的映射单元

### column

一列数据，由一个column对象表示

分为接口和实现两个部分

#### column对象

采用泛型的设计思路

* 定义了对数据各种关系运算的方法，例如插入数据，分页的cut，过滤的filter等
* 这些方法的实现，则根据数据类型的不同，由相应的对象实现
* 大多数情况下，都是以整列的数据进行操作

### field

采用聚合的设计思路

* file对象代表一个单值
* 内部聚合了Null, Uint64, String, Array等13种数据类型及对应的处理逻辑


## DataType

数据的序列化和反序列化

* datatype定义了许多正反序列化的方法，它们都是才对出现
* 采用了泛型的设计模式，具体方法的实现逻辑由对应数据类型的实例承载
* 覆盖了二进制，文本，JSON，XML，CSV，Protobuf等类型

### 特点

* 虽然datatype负责序列化的工作，但是并不会数据的读取，而是转由column对象和field对象获取


## Block与Block流

clickhouse里面的数据操作是面向Block对象进行的，而且采用流的形式

* 虽然column和field组成了数据的基本映射单元，但是到了实际操作，还是缺少一些必要的信息

Block对象可以看作是数据表的子集

Block对象的本质是个三元数组

* 数据对象----------------column对象
* 数据类型----------------Datatype
* 列名称-------------------列名称字符串

column对象提供了数据读取能力，datatype知道如何正反序列化

### Block流

* block流也采用了泛型的设计，对数据的各种操作最终都会转化成一种流的实现

有了block对象的这一层封装，流操作就很自然的事情

#### IBlockInputStream

* 负责数据的读取和关系运算

#### IBlockOutputStream

* 负责将数据输出到下一环节

## Parser与Interpreter

### Parser分析器

* 负责创建AST对象
* 可以将一条SQL语句，以递归下降的方法解析成AST语法树的形式
* 不同的SQL语句由不同的parser实现类解析

### InterPreter解释器

* 负责解释AST，并进一步创建查询的执行管道
* 解析AST对象，然后执行相关操作，返回IBlock对象，以线程的形式建立起一个查询执行管道

## Functions与Aggregate Functions

### 普通函数

* 普通函数由IFunction接口定义
* 普通函数是没有状态的，函数效果作用每行数据之上
* 执行过程中，并不会一行一行的执行，而是采用向量化的方式直接作用于一整列数据
* * 四则运算
  * 日期转换
  * 网址地址提取
  * IP地址脱敏函数


### 聚合函数

* 聚合函数由IAggregateFunction接口定义
* 聚合函数是有状态的，聚合函数的状态支持序列化和反序列化

## Cluster和Replication

* clickhouse的一个节点只能拥有一个分片
* 如果要实现一分片，一副本，需要两个节点
* 分片只是一个逻辑概念，真实承载还是在副本

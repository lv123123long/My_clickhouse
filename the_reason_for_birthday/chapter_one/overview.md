# 概述

Click Stream Data  WareHouse 简称：clickhouse

### 不适合的场景

* 不支持事务
* 不擅长根据主键按行粒度进行查询，，不能当初key-value数据库使用
* 不擅长按行进行删除

## 架构

* 没有采用hadoop的主从架构，而是采用了多主对等网络结构
* clickhouse是一款采用MPP架构的列式存储数据库

## DBMS功能

### DDL：数据定义语言

* 可以动态的创建，修改或者删除数据库，表，视图，无需重启服务

### DML：数据操作语言

* 可以动态查询，插入，修改和删除数据

### 权限控制

* 可以根据用户粒度进行设置数据库或者表的操作权限

### 数据备份和恢复

* 提供了数据备份导入和导出恢复机制

### 分布式管理

* 提供集群模式，能够自动管理多个数据库节点

## 列式存储和数据压缩

减少数据扫描范围和数据传输时的大小，可以加快数据查询性能

### 列式存储

* 有助于减小数据查询范围

### 数据压缩

* 按照一定步长对数据进行匹配扫描，遇到相关内容进行编码转换

#### LZ4算法压缩

## 向量化执行引擎

* 为了实现向量化执行，需要用到CPU的SIMD指令，即单挑指令操作多条数据
* 通过数据并行以提高性能的一种实现方式

## 关系模型和SQL查询

### 多样化的表引擎

* 将表引擎独立设计出来，，，适应更多的场景

## 多线程和分布式

计算移动比数据移动更划算

* 预先将数据分布到各台服务器，将数据的计算查询推到数据所在的服务器
* 在数据存取方面，支持分区（纵向扩展，利用多线程原理）
* 在数据存取方面。也支持分片（横向扩展，利用分布式原理）

## 数据分片和分布式查询

数据分片是将数据进行横向切分

分片依赖于

* 每个集群由一个或者多个分片组成
* 每个分片对应于clckhouse的一个服务节点，分片数据取决于节点数量

### clickhouse就像手动挡一样

* 没有提供高度自动化的分片功能
* clickhouse提供了本地表和分布式表的概念

#### 本地表

一张本地表等同于一份数据的分片

#### 分布式表

* 分布式表，本身不存储数据，而是本地表的访问代理，类似y

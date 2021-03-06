---
title: mysql查询性能优化
date: 2019-05-29 15:31:47
tags: 
 - mysql
 - db
 - 数据库
---

### MySql优化-查询优化

> mysql的优化主要分为：查询优化+索引优化+库表解构优化等起头并进。

#### 本博文仅对查询优化进行梳理总结

+ 为什么查询慢
+ 优化数据访问
+ 重构查询方式
+ 查询执行基础
+ 查询优化器

---

#### 一、为什么查询慢

   <font size=2>查询的生命周期： 客户端>服务器>服务器解析查询>生成执行计划>执行>返回客户端 


   造成查询慢的主要因素：包括网络，cpu计算，生成统计信息和执行计划，所等待（互斥等待）等操作，向底层存储引擎检索数据的调用操作。


   总结：把查询当成一个任务，对一系列的子任务进行优化。


#### 二、优化数据访问

   <font size=2>检索大量超过需要的数据（太多行和列）例如：我们的分页limit就是优化检索数据行

   尽量少用SELECT *，精确到指定的列或者关联的主表字段

   响应时间的影响因素：存储引擎的锁（表锁，行锁）、高并发资源竞争、硬件响应等。推荐"快速上限估计"法来评估响应时间是否合理

   扫描行数和返回行数比率越小越好。在1:1~10:1之间

   对扫描行数寻找合适的访问类型，访问类型需要了解自扫描表、扫描索引、范围访问和单值访问的概念。通过EXPLAIN结果可以查看访问类型


#### 三、重构查询方式

   <font size=2>分而治之，将大查询分解成小查询，如果是事物引擎可以提高效率，减少服务器一次处理成本和压力，减少锁持有时间

   <font size=2>分解关联查询可以让缓存效率更高，减少锁的竞争，减少记录冗余查询，在应用层面做关联查询提高性能便于扩展，减少重复访问数据，相当于实现了哈希关联


#### 三、查询执行的基础（查询过程）

1. 客户端发请求给服务器
2. 服务器先检查查询缓存，如果有缓存，立即返回缓存结果，否则进行下一阶段
3. 服务器进行SQL解析，预处理，再有优化器生成对应执行计划
4. mysql根据执行计划生成查询执行引擎，调用存储引擎的API接口执行查询
5. 返回结果给客户端
---
   <font size=2>Mysql客户端服务器之间通信采用半双工通信协议，通过设置max_allowed_packet控制查询太大，服务器端拒收更多数据抛出错误

   <font size=2>Mysql的查询状态，每一个mysql链接都可看做一个线程，任何时刻都有一个状态，可以通过show full processlist命令查看

1. sleep: 线程等待客户端发送新请求
2. query：线程正在执行查询或者将结果返回给客户端
3. locked: 服务器层，该线程正在等待锁(表锁，行锁)
4. analyzing and statistics: 线程正在收集存储引擎的统计信息，并生成查询执行计划
5. copying to tmp table [on desk] 线程正在执行查询，并将结果复制临时表，一般用于group by操作或者文件排序，union等，内存临时表如果标记[on desk]标识放到磁盘上
6. sorting result：线程正在对结果集进行排序
7. sending data：线程可能做多个状态间传递数据，或者生产结果集或者向客户的返回数据
---

   <font size=2>查询缓存query cache计算查询语句哈希值，移除注解；检查是否命中缓存通过一个对大小写敏感的哈希查找实现，如果当前查询命中缓存，直接返回结果。


#### 四、查询优化器（重点）

   <font size=2>查询优化器在进行语解析和预处理后，找到最好的执行计划。

   Mysql能够处理的优化类型：
1. 重新定义关联表顺序
2. 将外连接转内连接
3. 使用等价变换规则
4. 优化count() min() max() 通过查询对于B-Tree索引来优化
5. 预估并转化为常数表达式
6. 覆盖索引扫描
7. 子查询优化
8. 提前终止查询
9. 等值传播
10. 列表IN()的比较，mysql会对IN()数据先排序再进行二分查找，查询复杂度O(log n)相对于OR复杂度O(n)速度更快




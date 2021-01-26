---
title: JavaScript内存
date: 2021-01-26 11:07:33
tags:
  - JavaScript
  - 前端
  - 浏览器
---

> 前言
在小猪面试中，被面试官问到一个问题,javascritp的原始值和引用值在内存中如何存放？

### javascript内存大家都知道分为堆内存和栈内存

- 什么是堆内存？
堆(heap)和数据结构堆二者没有太深的关系，同名而已。是一片动态不连续的内存空间,访问速度比栈内存慢。适合存放时间周期较长，占用空间较大或者占用空间不固定的数据。

- 什么是栈内存？
栈(stack)是一种数据结构,遵循LIFO原则。是一段连续的内存空间，访问和读取速度比堆内存快，容量较小。适合存放生命周期短，占空间小的固定数据。

### javascript中存储变量

- 原始类型(primitive type)
原始类型也叫原始值或者基本类型: string number boolean null undefined symbol等
原始类型数据存储在栈内存

- 引用类型(reference type)
引用类型：Object Array Function Date RegExp String Number Boolean等
引用类型数据存储在堆内存

*注意* 全局变量以及闭包引用的变量是存储在堆内存的。

### javascript不可变原始值和可变对象引用

上面提到原始值：当我们定义一个原始类型变量的时候，javascript会激活一块栈内存存储，当修改原始值时，会再激活一块栈内存存储新值并将变量指向新值。而不是改变原始值那块栈内存的值【栈内存中的原始值一旦确定就不能被修改】

![示例](/assets/img/memory.jpg) 

上面提到的引用类型，我们在堆内存中存储对象，在栈内存中存储对象的引用(堆内存地址)，当把引用类型变量赋值给另一个变量，就是复制了对象的引用(堆内存的地址)并未在堆内存中生成一个新对象。当直接修改变量指向，会给对象引用指向堆内存的新对象。【堆内存中对象的引用是可以被修改的】

![示例](/assets/img/memory2.jpg) 

*作业: * 实现对象的深拷贝？

### javascript内存的声明周期
- 垃圾回收
- 可达性
- 内存泄露

### javascript垃圾回收算法
- 标记删除(mark and sweep)
- 引用计数(reference counting)
- 循环引用(circular references)
- 双管齐下

*作业：* 什么是闭包，闭包对内存性能影响？

### 如何查看chrome中内存使用情况？
- Memory in Devtools(内存面板) 
+ 1、堆快照(Heap snapshot): 记录了当前页面当前时刻JS对象及DOM节点内存分配
+ 2、内存分配时间轴(Allocation instrumentation on timeline)在一段时间内持续记录内存分配
+ 3、内存分配采样(Allocation sampling)使用采样方法记录内存分配，开销小，可用于长时间记录
+ 4、堆内存占用
+ 5、前两分钟内的内存占用变化趋势
+ 6、实例名称
+ 7、堆内存总占用大小
+ 8、前两分钟内内存中占用变化趋势
![示例](/assets/img/memory-tool.png) 

- 堆快照列表
+ 1、视图列表(summary摘要视图、comparsion比较视图、containment包含视图、statistics统计视图)
+ 2、根距离(Distance)对象与GC根之间的最短距离
+ 3、浅层大小(shallow size)当前对象自身占用内存大小，不包含引用对象单位bytes字节
+ 4、保留大小(retained size)当前对象被GC回收后总共能释放的内存大小单位bytes字节
+ 5、持有者(retainers)直接引用目标对象的变量
![示例](/assets/img/snapshot.png) 

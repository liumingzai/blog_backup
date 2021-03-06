---
title: var a=2都做了什么？
date: 2020-11-20 10:28:02
tags:
  - javascript
  - 作用域
  - 执行环境
  - 前端
---

> var a = 2 都发生了什么呢？那么我们要先理解引擎、编译器、作用域的原理

引擎：从头到尾负责执行整个JavaScript程序的编译及执行过程。

编译器：负责语法分析及代码生成等脏活累活。

作用域：负责收集并维护由所有声明的标识符组成的一系列查找，并实施一套非常严格的规则，确定当前指向的代码块对这些标识符的访问权限。

> 内部原理经理五个过程：编译、执行、查询、嵌套、异常

####　编译

在传统的变成语言中，一段代码一般在执行之前会经历三个步骤：分词、解析、代码生成。

+ 分词(tokenizing) 把var a = 2; 的字符串分解成有意义的代码块，这些代码块成为词法单元
+ 解析(parsing) 把词法单元流转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树（抽象语法树AST）
+ 代码生成 将AST转换成可执行的代码，也就是将var a=2;的抽象语法树转为一组机器指令

#### 执行过程

  编译原理是把程序分解成词法单元(token),然后把词法单元解析成抽象语法树(AST),再把抽象语法树变成可执行的机器指令，编译执行过程是怎样呢？

  编译过程：编译器查找作用域是否由a这个变量且存在同一个作用域，存在直接编译，不存在再当前作用域集合中声明一个变量a
  执行过程：引擎运行时先查当前作用域是否存在a的变量。存在直接赋值2，不存在继续查找，找不到抛出异常。

#### 查询

  在引擎执行时先对变量a进行查询(LHS查询) LHS和RHS查询区别取决于变量出现在赋值操作左侧还是右侧

#### 嵌套

  当引擎查询不到变量就会在外层桥套的作用域种继续查找，一直到最外层作用域（全局作用域）为止

#### 异常

  RHS和LHS两种查询在变量还没有声明情况下，查询结果时不同的

  #### 使用RHS时，如果变量未声明会抛出ReferenceError xx is not define

  function rhs(xx){
    xx = yy;
  }
  rhs();  //ReferenceError: xx is not defined

  #### 使用LHS查询时，如果无法找到变量，会在全局作用域种创建一个

  function lhs(){
    xx = 1;
  }
  lhs(); //xx:1

  #### 使用LHS查询时如果时严格模式，如果午发找到一个变量会抛出错误

  'use strict'
  function lhs(){
    xx = 1;
  }
  lhs(); //ReferenceError: xx is not defined

  
  #### 总结： 区分LHS查询和RHS查询很重要,对于词法作用域和动态作用域可以参考

  (转载javascript作用域)[https://www.cnblogs.com/xiaohuochai/p/5700095.html#anchor2]

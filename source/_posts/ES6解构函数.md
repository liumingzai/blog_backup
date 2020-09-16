---
title: ES6解构函数
date: 2020-06-23 09:46:10
tags:
  - 对象
  - 数组
  - 参数解构
  - ES6
---


> 起源:了解解构函数源于一道面试题：变量交互let x=1; let y=1; [x,y]=[y,x]

### 什么是解构（Destructuring）

  + 将一个数据结构分解开分别进行赋值,ES6允许按照一定模式,从数组和对象中提取值，对变量进行赋值。

  + 原理：结构是ES6提供的语法糖，针对可迭代对象的Iterator接口，通过遍历器按顺序获取对应的值进行赋值


#### 数组解构

``` bash
// 基本类型
let [x, y, z] = [1, 2, 3]

// 对象数组 
let [a, b, c] = [{name: 'a'}, {name: 'b'}, {name: 'c'}]

// ...扩展运算符
let [a, ...b] = [1, 2, 3, 5]

// 嵌套
let [a, [b]] = [1, [2, 4]]

// 解构默认值
let [a=1, b=2, c] = [0, , 3]


```

#### 对象解构

``` bash
// 对象属性结构
let {o1, o2} = {o1: 'first', o2: 'last'}

// 解构重命名
let {firstName: familyName, givenName} = {givenName: 'mingzai', firstName: 'liu'}

// 嵌套解构及默认值
let {number,user: {name}} = {number:1, user:{name: 'liumingzai', age: 30}}

// 解构别名使用
let  {first_name: familyName} = {first_name: 'liu' , given_name: 'mingzai'} 

```
#### 函数参数解构
``` bash

function caculate(...param){
 var sum=0;   
 param.map((item)=>{
    sum+=item;  
  })
return sum;  

}
caculate(1,2,5);

```


#### 可迭代对象解构语法糖

``` bash

// String
let [ a, b, c, ...rest ] = 'test123'
console.log(a, b, c, rest) // t, e, s, [ 't', '1', '2', '3' ]

// Map
let [a, b] = new Map().set('f1', 'test1').set('f2', 'test2')
console.log(a, b) // [ 'f1', 'test1' ], [ 'f2', 'test2' ]

// Set
let [a, b] = new Set([1, 2, 3])
console.log(a, b) // 1, 2

```


---
title: reduce为何成了高逼格
date: 2020-11-12 10:41:33
tags:
  - es6
  - 前端
  - reduce
---

> reduce在我们实际开发中的高逼格应用

+ reduce的语法按我的理解其实是一个callback回调包含四个参数，以及一个初始值initalValue

+ 四个参数分别是: previousValue、currentValue、index、array。我觉得需要强调的可能有第一个参数，表示上一次回调返回值或者initialValue。第三个参数index是从1开始的，如果要从0开始可以设置initalValue为0

#### 常用来回答一套传统的面试题，求数组元素的和（工作好几年的我第一个印象还是循环）那么高逼格的写法是如何的呢？

``` bash
  
  let arr = [1,2,3,4,5];
  let sum = arr.reduce(function(prev, cur, index, arr){
    console.log(prev, cur, index);
    return prev+cur;  // pre上一次回调返回值
  })
  console.log(sum);  //15

  运行结果：
  1 2 1
  3 3 2
  6 4 3
  10 5 4
  15

```

#### 可能有人和我一样，觉得这个initailValue又什么用呢？我们对比一下下面的代码

``` bash

  let arr = [];
  let sum = arr.reduce(function(prev, cur){
    return prev+cur; 
  })
  console.log(sum);  //报错，"TypeError: Reduce of empty array with no initial value"

  let arr = [];
  let sum = arr.reduce(function(prev, cur){
    return prev+cur;  
  }, 0)
  console.log(sum);  // 0


  ```

#### 上面简单的reduce用法已经介绍完了，那么我们看看如何玩转reduce，从几个算法面试题入手

+ 第一道算法题：获取数组中每个元素重复次数（返回一个对象，initalValue定义为空对象）

``` bash
  let arr = ['banana', 'orange', 'banana', 'grapes', 'melon', 'orange','pear'];
  let result = arr.reduce(function(pre, cur) {
    if(cur in pre){
      pre[cur]++;
    }else{
      pre[cur]=1;
    }
    return pre;
  }, {})

  console.log(result);

  运行结果：
  { banana: 2, orange: 2, grapes: 1, melon: 1, pear: 1 }

```
+ 第二道算法题：数组去重(返回一个新数组，initalValue定义为空数组)

``` bash
  let arr = ['banana', 'orange', 'banana', 'grapes', 'melon', 'orange','pear'];
   let result = arr.reduce(function(pre, cur){
     if(!pre.includes(cur)){
       return pre.concat(cur);
     }else{
       return pre;     
     }
  },[])
  console.dir(result)

  运行结果：
  [ 'banana', 'orange', 'grapes', 'melon', 'pear' ]

```

+ 第三道算法题：将二维数组转换为一维有序数组

``` bash
  let arr =[[1,2,4],[2,3,7],[3,5,7],[4,5,8]];
  let result = arr.reduce(function(pre, cur){
       return pre.concat(cur);
  },[]).sort((a,b) => a-b)
  console.log(result);
  
  运行结果：
  [ 1, 2, 2, 3, 3, 4, 4, 5, 5, 7, 7, 8 ]

```
+ 第四道算法题：将多维数组转换为一维有序数组(偏平化)

``` bash

  let arr =[[1, [2, [3,7],[5,7]]]];
  let result = function(arr) {
    return arr.reduce((pre, cur) => pre.concat(Array.isArray(cur)? result(cur): cur),[])
  }
  console.log(result(arr));

  运行结果：
 [ 1, 2, 3, 7, 5, 7 ]

```

+ 第五道算法题：生成裴波那契数列

``` bash

  const fibonacci = (n) => {
    return Array(n).fill(0).reduce((pre, cur,i)=> {
      console.log(pre, cur, i)
      return pre.concat(i>1 ? pre[i-1]+pre[i-2]: i) 
    } ,[])
      
  } 
  console.log(fibonacci(5))
  
  运行结果：
  [] 0 0
  [ 0 ] 0 1
  [ 0, 1 ] 0 2
  [ 0, 1, 1 ] 0 3
  [ 0, 1, 1, 2 ] 0 4
  [ 0, 1, 1, 2, 3 ]

```

+ 第六道算法题：过滤对象key属性

``` bash
  const pick = (obj, arr)=>
    arr.reduce((pre, cur)=> {
      // if(cur in obj) {
      if(Object.keys(obj).includes(cur)){
        pre[cur] = obj[cur]
        return pre;
      }
    }, {})

  console.dir(pick({id: 12, name: '张三' , age:24}, ['id', 'name']));

  运行结果：
  { id: 12, name: '张三' }

```


+ 第七道算法题：分组聚合

``` bash


  运行结果：
  

```

#### 其实reduce操作不局限于数组，只要可枚举的类型都适用

``` bash
  let personIncome = [{category:"salary", value: 2000}, {category:"performace", value: 500},  {category:"benefits", value: 500}];
  let payment = personIncome.reduce((pre, cur)=> {
    return pre + cur.value;
  }, 0);
  console.log(payment);


  运行结果：
  3000

``` 

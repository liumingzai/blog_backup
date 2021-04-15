---
title: 闭包closure
date: 2020-11-20 15:46:58
tags:
  - javascript
  - closure
  - 前端
---


> 说起闭包，大家都耳熟能详因为是大中小厂面试必考。但是其原理和用途却很少有人能讲清楚

  a closure is an inner function that has access to the outer（enclosing）function‘s variables —— scope chain


#### 闭包都做了什么事情？

1. 闭包存储了外部变量的引用
2. 可以修改外部定义的变量值

![闭包](/assets/img/closure.png) 


#### IIFE是闭包吗？

严格来讲，IIFE(Imdiately Invoked Function Expression)不是闭包，但是也拥有闭包的特性。
IIFE避免---function出现行首，让引擎误认为是函数声明； 常见的IIFE 
 + (function (){}());  
 + (function (){})();
 !function(){}();
 +function(){}();

 var foo = function(){}();
 
 * 注意function foo(){}(); 报错"SyntaxError: Unexpected token" 报错原因函数声明和分组操作符组合后，分组操作符不能为空
   function foo(){}(1) 不报错返回1

 * 立即执行函数和普通函数

  ``` javascript
    // with
    var a = 0;
    with(a = 1) {
      function f() {
        console.log(a);  //1
      }

      (function(){
        console.log(a);  //1
      })()
      f();
    }

    //try catch
    try{
      var e = 1;
      throw new Error();
    }catch(e){
         function f() {
        console.log(e);  //Error
      }

      (function(){
        console.log(e);  //Error
      })()
      f();
    }
  ```

 * 具名函数表达式

  ``` javascript

  (function(){
    a = 1;
    console.log(a);   // 输出1
  })()

  (function a(){
    var a = 1;
    console.log(a);  //输出1
    console.log(window.a) // undefined
  })()

  // 具名函数表达式(NFE)特性
  //a不能再绑定其他值(不能被修改)所以a=1无效，但是严格模式会报错Uncaught TypeError:Assigment to constant 
  (function a(){
    a = 1;
    console.log(a);  //输出function a(){a = 1;console.log(a)}  
    console.log(window.a) // undefined
  })()


 ```

 * 立即执行函数避免了全局污染
  ``` javascript
    var inc = (function(){
      var cnt = 0;
      return function(){
        ++cnt;
      }
    })();

 ```

#### 闭包对执行环境的影响

``` javascript
  function foo() {
    var a = 1;
    function bar() {
      console.log(a);
    }
    return bar;
  }

  var baz = foo();
  baz();
  baz = null;

```

不难发现，上面代码由于闭包bar()函数的引用了foo()的变量a，导致foo()虽然执行环境销毁了，但其变量一直在内存，等待bar()的引用，从活动状态变成非活动状态。bar()函数执行时通过 bar()->foo()->全局作用域查找a。直到页面关闭，变量a才会和全局对象一起销毁。释放内存空间。所以要谨慎使用闭包，尽量使用完闭包，及时解除引用如：baz = null;


####　闭包与循环

``` javascript

  function foo() {
    var arr = [];
    for(var i=0;i<5;i++) {
      arr[i] = function(){  //关键：并没有把函数返回值赋给数组，而是吧函数赋给数组
        return i;
      }
    }
    return arr;
  }
  var bar = foo();
  console.log(bar[0]());  //5 

```
使用闭包优化


``` javascript
  function foo() {
    var arr = [];
    for(var i=0;i<5;i++) {
      arr[i] = (function fn(j){
        return function(){
            return j;
        }
      })(i)
    }
    return arr;
  }
  var bar = foo();
  bar[0](); // 0

```
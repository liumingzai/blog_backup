---
title: 箭头函数this指向
date: 2021-02-23 10:03:08
tags:
  - es6
  - javascript
---

> **箭头函数this指向**是面试高频词，虽然只是一个小的知识点，很容易混淆，所以有必要去学习和复盘。遂写此文

### 普通函数this指向

**函数this**
``` javascript

  /**
   * 声明式函数（严格模式也返回undefined）
   * */
  function fun() {
    var a = 1;
    consoel.log(this.a);  // 在非严格模式下this指向window,a是局部变量，所以返回undefined
  }

  /**
   * 函数表达式
   * */
  'use strict'
  var fun = function() {
    consoel.log(this); // this指向undefined
  }

```
**对象中的this**
``` javascript
  const obj = {
    x: 1,
    say: function() {
      console.log(this);  // this指向obj,指向调用这些方法的对象
    }
  }
```
**for和forEach循环中的this**
``` javascript
  ['a','b'].forEach(function(){
    console.log(this);  // this指向window
  })
```
**setTimeout和setInterval中的this**
``` javascript
  const obj = {
    test: function() {
      console.log(this);   //this指向obj
      setTimeout(function(){
        console.log(this);  //this指向window，
      }, 1000)
    }
  }
  
  obj.test();
```
> 注意：第一个this指调用test的对象obj， 第二个this指向window，因为setTimeout里的匿名函数没有调用对象，默认指向window

**#### **原型链中的this** [原型继承可关注我的JS原型基础篇博文](https://liumingzai.github.io/2020/11/25/JS%E5%8E%9F%E5%9E%8B%E5%9F%BA%E7%A1%80%E7%AF%87/)
``` javascript
  var cup = {
    name: "",
    drink: function(){
      return "drink " + this.name;
    }
  }

 var coffeeCup = Object.create(cup);
 coffeeCup.name = "coffee";
 coffeeCup.drink();  // this指向调用对象coffeeCup而不是cup

```
**构造函数中的this**构造函数基础可以看《JavaScript高级程序设计》6.2.2
``` javascript
  /**
   * new操作符调用构造函数发生了什么？
   * 1.创建一个新对象；
   * 2.将构造函数的作用域赋给新对象(因此this就指向了这个新对象)；
   * 3.执行构造函数中的代码；
   * 4.返回新对象；
   * */
  function Foo() {
    console.log(this); //this指向一个新对象Foo {}, 
    return {x:1};   //this指向{x:1}  
  }

  console.log(new Foo().x);  //1

  //当return一个空对象，this指向返回对象
  function Foo() {
    this.x = 1;
    return {};
  }
  console.log(new Foo().x) //undefined

  //当return不是对象，this还是指向new Foo()实例
  function Foo() {
    this.x = 1;
    return undefined;
  }
  console.log(new Foo().x) //1


```
> 注意3和4，是先修改this指向，在执行构造函数中的代码

**内联事件绑定this指向**
``` bash
 <!--this指向监听的dom-->
 <div onclick="alert(this);">click me</div>
 
 <!--this指向window|undefined同上文中严格模式下-->
 <div onclick="(function(){alert(this)})();">click me</div>
 
```

### 箭头函数this指向
+ 默认指向定义它时，所处上下文对象的this指向。没有上下文对象，默认指向this
+ 即使通过call、apply、bind等方法也不能改变箭头函数的this指向


``` javascript
  // 先来做个对比
  const obc = {
		x:1,
		say: function(){
			 setTimeout(function(){console.log('2', this);});  //this指向window
			 console.log('1', this);   //this指向obc
		}
	}

  obc.say();
	
	const obc = {
		x:1,
		say: ()=>{
			 setTimeout(()=>{console.log('2', this);});  //this指向window
			 console.log('1', this);  //this指向window
		}
	}
  
  obc.say();

```
> 注意：say是箭头函数，this指向上下文函数中的this指向，这里上下文没有函数对象，所以指向window

``` javascript

  document.addEventListener('click', function(){
    console.log(this); // this指向document对象
  });

  document.addEventListener('click', ()=>{
    console.log(this); // this指向window
  });

```

``` javascript

  const obj = {
    fun1: function() {
      console.log(this);   //this指向obj
    },
    fun2: function() {
      const fun3 = ()=> {
        console.log(this);   //this指向obj,因为上下文函数对象fun2指向obj
      }
      fun3();
    }
  }

```

### 如何改变this指向
+ 箭头函数不能修改this指向
+ 普通函数修改this指向的方法有call、apply、bind

> 注意：call和apply基本相同，仅apply只接收两个参数，第二个参数是数组，二者都是立即执行函数而bind不是立即执行函数，并且声称一个新函数,bind的实现会在后面章节更新

``` javascript
 
   function Foo() {
    this.name = "foo";
    setTimeout(function() {
      console.log(this);  //指向window
    },1000);
  }
  
  var f = new Foo();

  //通过bind修改this指向
  function Foo() {
    this.name = "foo";
    setTimeout((function() {
      console.log(this);   //指向f
    }).bind(this),1000);
  }
  
  var f = new Foo();

```
**总结**
写过jquery的同学都应该写过_this = this或者_that = this避免this指向变化
在严格模式下this不是指向window而是undefined
我们做个练习巩固一下

``` javascript

  const obj1 = {
    a: 1,
    b: {
      func: function(){
        console.log(this); //指向obj.b
        console.log(this.a); //undefined
      }
    }
  }

  obj1.b.func();

  // this指向只有在函数执行时才能确定
  const obj2 = {
    a: 1,
    b: {
      func: function(){
        console.log(this); //指向window
        console.log(this.a); //undefined
      }
    }
  }

  var t=obj2.b.func;
  t();

  
```



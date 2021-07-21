---
title: JS基础回顾
date: 2021-07-19 09:52:52
tags:
  - JavaScript
---

> 不管你身处什么困境，只要你不放弃，你就能找到你存在的价值。不枉此生，努力学习

总结了这篇文章，为了巩固自己基础，也方便入门前端的童鞋一起学习

## 什么是对象，对象有哪几类？

对象的本质: 无序的键值对集合;键值对当中的值可以是任意数据类型的值；可以理解为对象是一个容器，这个容器当中存放（属性和方法）

对象分类：
+ 本地对象-独立于宿主环境(浏览器)的对象(Object, Array, Boolean, Number, String, Function, RegExp, Date, Error)
+ 内置对象-使用时不需要new的对象(Math, Global-window)
+ 宿主对象-DOM、BOM

## 什么是原型和原型链？

#### 构造函数、实例对象、Function对象和原型对象之间是什么关系呢？
1.构造函数的prototype属性其实是Object的实例
  ``` js
    Foo.prototype.__proto__ === Object.prototype
  ```
2.实例对象的__proto__属性指向构造函数的原型对象
  ``` js
    f.__proto__ = Foo.prototype
  ```
3.原型对象都有一个constructor属性，指向原型对象所属的构造函数
  ``` js
    Foo.prototype.constructor === Foo
  ```
4. Function --所有函数都是Function的实例
  ``` js
    Foo.__proto__ === Function.prototype
  ```
5. 函数一定是对象，但对象不一定是函数
  ``` js   
     Object.__proto__ === Object.prototype
  ```
#### prototype 和 __proto__它们是什么关系呢?
   + prototype 是构造函数的属性
   + __proto__ 是实例对象的属性
  
  ``` js
    //  二者都指向同一个对象。
    function Foo() {}
    var f = new Foo();
    f.__proto__ === Foo.prototype 
  ```

*总结：根据原型指向进行属性搜索的链路；例如在访问某个对象的某成员时，先在对象中查找，如果没有去构造函数原型对象中找
如果还没有就去对象原型上找，知道对象的原型是null为止*

## js如何实现继承？
+ 对象冒充闭包
  ```js
  function Role(role) {
    this.role = role;
    this.getRole = function() {
      console.log('role:', this.role);
    }
  }

  function Parent(name) {
    this.name = name;
    this.getName = function() {
      console.log('name:', this.name);
    }
  }


  function Child(name, pass, role) {
    this.pClass = Parent; //定义临时属性，指向Parent
    this.pClass(name);  //执行Parent(name)
    delete this.pClass;

    this.rClass = Role;
    this.rClass(role);
    delete this.rClass;

    this.getPass = function() {
      console.log('pass', pass);
    }
  }
  var c = new Child('admin', 123, 'guess');
  c.getName();
  c.getPass();
  c.getRole();
  ```
+ call()方法方式
  + call方法是Function类中的方法
  + call方法的第一个参数的值赋值给类(即方法)中出现的this
  + call方法的第二个参数开始依次赋值给类(即方法)所接受的参数

  ```js
  function Role(role) {
    this.role = role;
    this.getRole = function() {
      console.log('role:', this.role);
    }
  }

  function Parent(name) {
    this.name = name;
    this.getName = function() {
      console.log('name:', this.name);
    }
  }


  function Child(name, pass, role) {
    Parent.call(this, name);
    Role.call(this, role);

    this.getPass = function() {
      console.log('pass', pass);
    }
  }
  var c = new Child('admin', 123, 'guess');
  c.getName();
  c.getPass();
  c.getRole();
  ```
+ apply()方法方式
  + A、第一个参数与call方法的第一个参数一样，即赋值给类(即方法)中出现的this
  + B、第二个参数为数组类型，这个数组中的每个元素依次赋值给类(即方法)所接受的参数
  
  ```js
  function Role(role) {
    this.role = role;
    this.getRole = function() {
      console.log('role:', this.role);
    }
  }

  function Parent(name) {
    this.name = name;
    this.getName = function() {
      console.log('name:', this.name);
    }
  }

  function Child(name, pass, role) {
    Parent.apply(this, [name]);
    Role.apply(this, new Array(role));

    this.getPass = function() {
      console.log('pass', pass);
    }
  }
  var c = new Child('admin', 123, 'guess');
  c.getName();
  c.getPass();
  c.getRole();
  ```

+ 原型链方式
  ```js

  function Parent(name) {
    this.name = name;
  }

  Parent.prototype.getName = function() {
    console.log('name:', this.name);
  }

  function Child(name, pass) {
    this.name = name;
    this.pass = pass;
  }

  Child.prototype = new Parent();  // 当new Child时得到的对象带有Parent对象所有方法

  Child.prototype.getPass = function() {
    console.log('pass:', this.pass);
  }

  var c = new Child('admin', 123);

  c.getName(); // parent方法
  c.getPass();

  ```
+ 混合方式(混合了call方式、原型链方式)
  ```js
  function Parent(name) {
    this.name = name;
  }

  Parent.prototype.getName = function() {
    console.log('name:', this.name);
  }

  function Child(name, pass) {
    Parent.call(this, name);  //继承父类属性
    this.pass = pass;
  }

  Child.prototype = new Parent(); //继承父类方法

  Child.prototype.getPass = function() {
    console.log('pass:', this.pass);
  }

  var c = new Child('admin', 123);

  c.getName();
  c.getPass();

  ```


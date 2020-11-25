---
title: JS原型基础篇
date: 2020-11-25 11:31:39
tags:
  - JavaScript
  - prototype
  - 继承
---

> 说起JS继承方式，有一种大家不陌生就是利用原型prototype来实现，来我们一起来看看js原型宝图

![js原型](/assets/img/prototype.jpg) 


如果你跟我一样，看半天没怎么绕明白，不要慌，一步一个脚印，用代码去校验一下就会豁然开朗。当然要配合着项目实战多练习，印象才会深刻

``` bash

    /**
    * 普通对象只有__proto__属性，且指向该对象构造函数的原型对象
    */

    var a = 1;
    console.log(a.__proto__  === Number.prototype);
    var a = "abc"
    console.log(a.__proto__ === String.prototype);


    /**
      * 函数对象的__proto__ 指向该对象构造函数的原型对象
      * 函数对象是特殊对象，可以看作Function()构造函数new出来的实例，函数Object也是如此
      */

    function Con() {}
    function con() {}
    console.log('构造函数__proto__ ', Con.__proto__ === Function.prototype)
    console.log('Object函数__proto__ ', Object.__proto__ === Function.prototype)
    console.log('普通函数__proto__ ', con.__proto__ === Function.prototype)

    /**
    * 构造函数new出来的实例对象
    */
    let f1 = new Con;
    let f2 = new Con;

    console.log('实例对象的__proto__属性指向该实例对象的构造函数原型对象 ', f1.__proto__ === Con.prototype)  // 实例对象的__proto__属性指向该实例对象的构造函数原型对象  true
    console.log('实例对象的构造函数：'+f1.constructor, f1.constructor === f2.constructor)  // 实例对象的构造函数：function Con() {} true
    console.log('实例对象没有构造函数',f1.hasOwnProperty('constructor')) // 实例对象没有构造函数 false
    console.log('实例对象的构造函数是从原型上继承来的'+ f1.__proto__.constructor, f1.__proto__.constructor === f2.__proto__.constructor) //实例对象的构造函数是从原型上继承来的function Con() {} true
    console.log(f1.__proto__.hasOwnProperty('constructor')) //true
    console.log('实例对象比较', f1===f2)  // false


    /**
    * 通过原型实现继承，并可以查看该属性是否是继承来的
    */
    Con.prototype.commonValue =  "common"
    console.log('实例对象继承原型对象，对其commonValue比较', f1.commonValue===f2.commonValue)  // 实例对象继承原型对象，对其commonValue比较 true
    console.log('实例对象是否有commonValue属性', f1.hasOwnProperty('commonValue')) // 实例对象是否有commonValue属性 false



    /**
    * 任何对象都可以看作通过Object构造函数new出来的
    */
    let o1 = new Object;
    let o2 = new Object;

    console.log('通过Object()函数new出来的实例', o1.__proto__ === Object.prototype)
    console.log('实例对象的构造函数：'+ o1.constructor, o1.constructor === o2.constructor) // 实例对象的构造函数：function Object() { [native code] } true
    console.log('实例对象没有构造函数',o1.hasOwnProperty('constructor')) // 实例对象没有构造函数 false
    console.log('实例对象的构造函数是从原型上继承来的'+ o1.__proto__.constructor, o1.__proto__.constructor === o1.__proto__.constructor) //实例对象的构造函数是从原型上继承来的function Object() { [native code] } true
    console.log(o1.__proto__.hasOwnProperty('constructor')) //true
    console.log('实例对象比较', o1===o2)  // 实例对象比较 false


    /**
    * Object.prototype作为实例对象，它的原型对象是null
    */
    console.log('Con.prototype如果作为实例，它的原型对象是:', Con.prototype.__proto__ === Object.prototype)
    console.log('Function.prototype如果作为实例，它的原型对象是:' , Function.prototype.__proto__ === Object.prototype)
    console.log('Object.prototype如果作为实例，它的原型对象是:', Object.prototype.__proto__ === null)
    console.log('Function的实例其构造函数是Function:', Function.prototype.constructor === Function)
    console.log('Function函数，是new Function的结果:', Function.__proto__ === Function.prototype)


    /**
    * 
    * Object和Con本身没有constructor属性，是继承Function.prototype.constructor
    */

    console.log(Function.prototype.constructor === Function)  // true
    console.log(Con.constructor === Function)  // true
    console.log(Con.hasOwnProperty('constructor'))  // false
    console.log(Object.constructor === Function)  // true
    console.log(Object.hasOwnProperty('constructor'))  //false
    console.log(con.constructor === Function)  // true
    console.log(con.hasOwnProperty('constructor'))  // false

```


> 是不是和我一样一脸懵逼，还没消化完。似懂非懂。来吧我们带着问题如何实现继承实战

1. 怎么实现原型链继承呢？(举个杯子的例子)
``` bash
  function Cup(name){
    this.name = name;
    this.use = function() {
      console.log('drink ', this.name);
    }
  }

  function TeaCup(){  }

  // 原型链继承关键
  TeaCup.prototype = new Cup()   
  TeaCup.prototype.name = 'tea'

  var s1 = new TeaCup()
  console.log(s1.use());  // drink tea
  console.log(s1 instanceof TeaCup)  //true 实例是子类实例
  console.log(s1 instanceof Cup) // true 实例也是父类实例

```
>> 优点: 父类新增原型法法，所有子类都能访问到。 缺点:来自原型对象的所有属性被所有实例共享,无法实现多继承，子类无法向父类传参。

2. 使用构造函数继承（举个动物例子）
``` bash
  function Animal(name, food){
    this.name = name;
    this.food = food;
    this.eat = function(){
      console.log(this.name, '吃', this.food);
    }
  }

  Animal.prototype.say = function(){
    console.log('say no');
  }

  function Cat(name, food){
    Animal.call(this);  // 相当于复制父类的实例属性，this指向Cat
    this.name = name;
    this.food = food;
  }

  var s1 = new Cat('兔子','萝卜')
  var s2 = new Animal
  console.log(s1.eat())  // 兔子 吃 萝卜
  console.log(s1.say())  // TypeError: s1.say is not a function
  console.log(s2.say())  // say no
  console.log(s1 instanceof Cat)  //true
  console.log(s1 instanceof Animal) //false
```
>> 优点: 子类可以向父类传参，可以实现多继承通过call多个父类对象  缺点: 无法实现函数复用，实例不是父类的实例，只能继承父类属性方法不能继承原型属性方法

3. 返回父类实例实现继承 （举个玩球类运动）
``` bash
  
  function Ball(name){
    this.name = name;
    this.play = function(){
      console.log('玩', this.name);
    }
  }

  function Basketball(name){
    var instance = new Ball();
    instance.name = name;
    return instance
  }


  var s1 = new Basketball('篮球')
  console.log(s1.play())  // 玩 篮球
  console.log(Basketball('篮球').play())
  console.log(s1 instanceof Basketball)  //false
  console.log(s1 instanceof Ball) // true

```
>> 优点: 构造函数等同于普通函数执行 缺点: 与2不同，s1是父类的实例，不是子类，不支持多继承

4. 组合1,2两类继承 (举个打卡机例子也是推荐的用法)
``` bash
  function Check(num){
    this.num = num;
    this.show = function(){
      console.log('打卡' + this.num + '次')
    }
  }

  function Zhang(num) {
    Check.call(this)
    this.num = num;
  }

  Zhang.prototype = new Check();
  Zhang.prototype.constructor = Zhang // 完整的原型对象是需要有construct

  var s1 = new Zhang(3)
  console.log(s1.show())  // 打卡3次
  console.log(s1 instanceof Zhang)  //true
  console.log(s1 instanceof Check) //true
```
>> 优点: 既可以传参，同为父类子类的实例。既可以继承父类也可以继承原型，满足函数复用， 不存在引用共享 缺点:

5. 寄生组合继承 (对4优化也是推荐的用法)
``` bash
  function Check(num){
    this.num = num;
    this.show = function(){
      console.log('打卡' + this.num + '次')
    }
  }

  function Zhang(num) {
    Check.call(this)
    this.num = num;
  }

  (function(){
    var Parent = function(){};
    Parent.prototype = Check.prototype
    Zhang.prototype = new Parent();
  })();


  var s1 = new Zhang(3)
  console.log(s1.show())  // 
  console.log(s1 instanceof Zhang)  //true
  console.log(s1 instanceof Check) //false
```
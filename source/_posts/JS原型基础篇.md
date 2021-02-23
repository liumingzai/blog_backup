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


### 如果你跟我一样，看半天没怎么绕明白，不要慌，一步一个脚印，用代码去校验一下就会豁然开朗。当然要配合着项目实战多练习，印象才会深刻

``` javascript

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
    let c1 = new Con();
    let c2 = new Con();

    console.log('实例对象的__proto__属性指向该实例对象的构造函数原型对象 ', c1.__proto__ === Con.prototype)  // true
    console.log('实例对象的构造函数是从原型上继承来的', c1.__proto__.constructor === c2.__proto__.constructor) //true
    console.log('实例对象的构造函数：', c1.constructor === c2.constructor)  // true
    console.log('实例对象没有构造函数',c1.hasOwnProperty('constructor')) // false
    console.log('实例原型链上有构造方法', c1.__proto__.hasOwnProperty('constructor')) //true
    console.log('实例对象比较', c1===c2)  // false


    /**
    * 给Con原型对象上增加方法，可以在实例中共享
    */
    Con.prototype.commonValue = function(){ console.log("hello");}
    console.log('实例对象继承原型对象，对其commonValue比较', c1.commonValue===c2.commonValue)  // true
    console.log('实例对象是否有commonValue属性', c1.hasOwnProperty('commonValue')) // false

    Con.prototype.name="";
    c1.name = "c1";  
    c2.name = "c2";
    console.log(c1.__proto__.name);  // ""


    /**
    * 任何对象都可以看作通过Object构造函数new出来的
    */
    let o1 = new Object();
    let o2 = new Object();

    console.log('通过Object()函数new出来的实例', o1.__proto__ === Object.prototype)
    console.log('实例对象的构造函数是从原型上继承来的', o1.__proto__.constructor === o1.__proto__.constructor) //true
    console.log('实例对象的构造函数：', o1.constructor === o2.constructor) //true
    console.log('实例对象没有构造函数',o1.hasOwnProperty('constructor')) // false
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

### 1. 怎么实现原型链继承呢？(举个杯子的例子)

``` javascript
  function Cup(name){
    this.name = name;
    this.use = function() {
      console.log('drink ', this.name);
    }
  }

  function TeaCup() {}
  function MilkCup() {}

  // 原型链继承关键：子类的原型为父类的一个实例对象
  TeaCup.prototype = new Cup()
  MilkCup.prototype = new Cup()     

  // 必须在指定子类型为父类型实例对象后定义
  TeaCup.prototype.name = 'tea'
  MilkCup.prototype.name = 'milk'

  var s1 = new TeaCup()
  var s2 = new MilkCup()
  console.log(s1.use());  // drink tea
  console.log(s2.use());  // drink milk
  console.log(s1 instanceof TeaCup)  //true 实例是子类实例
  console.log(s1 instanceof Cup) // true 实例也是父类实例

```

 优点: 父类新增原型法法，所有子类都能访问到。 缺点:来自原型对象的所有属性被所有实例共享(私有公有属性),无法实现多继承，子类无法向父类传参。

### 2. 使用构造函数继承（举个动物例子）

``` javascript

  function Animal(name, food){
    this.name = name;
    this.food = food;
    this.eat = function(){
      console.log(this.name, '吃', this.food);
    }
  }

  function Pet(name){
    this.name = name;
    this.getName = function(){
      console.log('宠物', this.name);
    }
  }

  Animal.prototype.say = function(){
    console.log('say no');
  }

  function Cat(name, food){
    Animal.call(this);  // 或者Animal.apply(this) 子类型构造函数的内部调用超类型构造函数，this指向Cat用于子类向父类传参 Animal.call(this, '狼', '兔子')
    Pet.apply(this); // 相当于this.Pet(name)
    // 构造函数注入参数
    this.name = name;
    this.food = food;
  }

  var s1 = new Cat('兔子','萝卜')
  console.log(s1.eat())  // 兔子 吃 萝卜
  console.log(s1.getName()) // 宠物 兔子
  console.log(s1.say())  // TypeError: s1.say is not a function 说明子类实例不是父类实例不能继承原型上的方法
  console.log(s1 instanceof Cat)  //true
  console.log(s1 instanceof Animal) //false

```

 优点: 子类可以向父类传参，可以实现多继承通过call|apply调用多个父类对象  缺点: 无法实现函数复用每个子类都有父类实例函数的副本，影响性能，实例不是父类的实例，只能继承父类属性方法不能继承原型属性方法

### 3. 返回父类实例实现继承 （举个玩球类运动）

``` javascript
  
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

 优点: 构造函数等同于普通函数执行 缺点: 与2不同，s1是父类的实例，不是子类，不支持多继承

### 4. 组合1,2两类继承 (举个打卡机例子也是推荐的用法)

``` javascript
  function Check(num){
    this.num = num;
    this.show = function(){
      console.log('打卡' + this.num + '次')
    }
  }

  function Zhang(num) { 
    Check.call(this)  //通过调用父类构造，继承父类的属性并保留传参  （调用了一次父类）
    this.num = num;
  }

  Zhang.prototype = new Check();  // 将父类实例作为子类原型，实现函数复用  （又调用一次父类）
  Zhang.prototype.constructor = Zhang // 完整的原型对象是需要有construct并修复其构造函数指向

  var s1 = new Zhang(3)
  console.log(s1.show())  // 打卡3次
  console.log(s1 instanceof Zhang)  //true
  console.log(s1 instanceof Check) //true
```

 优点: 既可以传参，同为父类子类的实例。既可以继承父类也可以继承原型，满足函数复用， 不存在引用共享 缺点: 调用了两次父类构造函数生成了两份实例

### 5. 寄生组合继承 (举例员工打卡对4优化也是推荐的用法)

``` javascript
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
    var Staff = function(){}; // 创建一个没有实例的方法类
    Staff.prototype = Check.prototype 
    Zhang.prototype = new Parent();  // 将实例作为子类的原型，这样避免初始化两次父类实例
  })();


  var s1 = new Zhang(3)
  console.log(s1.show())  // 
  console.log(s1 instanceof Zhang)  //true
  console.log(s1 instanceof Check) //false
```

### 6. ES6中继承实现（缺点就是兼容性问题，不过大部分浏览器都支持es6）

``` bash
  class Person {
    constructor(name,age) {
      this.name = name;
      this.age = age;
    }

    showName() {
      console.log('父类方法')
      console.log(this.name, this.age)
    }
  }

  let p1 = new Person('oldKitty', 99)
  console.log(p1)

  class Child extends Person {
    constructor(name, age, hobby) {
      super(name, age)
      this.hobby = hobby
    }

    showName() {
      console.log('子类方法')
      console.log(this.name, this.age, this.hobby);
    }
  }

  let c1 = new Child('kitty', 2, 'play')
  console.log(c1)
  c1.showName()

  运行结果：
  Person { name: 'oldKitty', age: 99 }
  Child { name: 'kitty', age: 2, hobby: 'play' }
  子类方法
  kitty 2 play

```


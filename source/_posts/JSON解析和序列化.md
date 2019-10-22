---
title: JSON序列化
date: 2019-10-22 12:30:34
tags:
  - JSON
  - JS
	- 前端 
---

> 本篇博主主要根据红宝书里讲到的JSON大家不常用到的功能，但是很实用的功能做介绍。

## 栏目
+ 语法
+ 解析与序列化
+ 实例
+ 总结

---

### 一、语法

不管你是前端还是后端或是全栈开发，或多或少都和JSON打过交道，当然语法方面我们都熟知有三种类型的值：
+ 简单值（字符串、数值、布尔值或者null甚至可能是undefined）
+ 对象（这个我就不多说了用{}包起来的）
+ 数组 (这个我也不卖弄了用[]包起来的)

<font color=#eb7350 size=2>值得注意的是JSON中的对象属性名任何时候都必须加双引号</font>

---

### 二 解析与序列化

JSON对象解析不用多说，用"."导航，或者[index]下标均可，就是javascriipt对象解析

序列化主要是两个方法: 

``` bash
  JSON.stringfy()  // 把JavaScript对象序列化JSON字符串
  JSON.parse()     // 把JSON字符串解析为原生的JavaScript值

```
上面大家都很熟悉，现在我们带着问题出发,去挖掘一下这两个方法的其他用途，下面内容都将使用定义好的objtest来做实例演示

var objtest = [{"title": "animal", "authors": ["monkey","pig"], "edition": 3, "year": 2011},{"title": "fruit", "authors": ["orange","apple"], "edition": 3, "year": 2012}];

1、我想序列化结果只包含显示title和year？

``` bash
JSON.stringify(objtest, ["title", "year"]);
// "[{"title":"animal","year":2011},{"title":"fruit","year":2012}]"

```

2、我想序列化objtestd,但是我想把authors显示为以逗号分隔,并且去掉edition？

``` bash
JSON.stringify(objtest, function(key,value){
	if (key == "edition") {
    	return undefined;
	}
	if(key == "authors"){
		return value.join(",");
    }else{
		return value;
    }
});
// "[{"title":"animal","authors":"monkey,pig","year":2011},{"title":"fruit","authors":"orange,apple","year":2012}]"
```

3、JSON.stringify输出是不包含任何空格字符或缩进，我想让她格式化显示。

``` bash
JSON.stringify(objtest, function(key,value){
	if (key == "edition") {
    	return undefined;
	}
	if(key == "authors"){
		return value.join(",");
    }else{
		return value;
    }
},"\t");  // 注意第三个参数一个转义字符的制表位tab，我们也可以传入数值标识空格个数，传入特殊字符分隔符，默认是插入了换行符，增强了可读性

结果如下: 
"[
	{
		"title": "animal",
		"authors": "monkey,pig",
		"year": 2011
	},
	{
		"title": "fruit",
		"authors": "orange,apple",
		"year": 2012
	}
]"
```

4、我想把objtest中的title取出来放到一个数组里？

``` bash
var objtest =[
    {
        "title": "animal",
        "authors": [
            "monkey",
            "pig"
        ],
        "edition": 3,
        "year": 2011,
        toJSON: function(){ return this.title}
    },
    {
        "title": "fruit",
        "authors": [
            "orange",
            "apple"
        ],
        "edition": 3,
        "year": 2012,
        toJSON: function(){ return this.title}
    }
];

JSON.stringify(objtest);
//"["animal","fruit"]"

```

5、我什么都不做就想单纯的格式化JSON？
``` bash
JSON.stringify(objtest,null,4);
//"[
    {
        "title": "animal",
        "authors": [
            "monkey",
            "pig"
        ],
        "edition": 3,
        "year": 2011
    },
    {
        "title": "fruit",
        "authors": [
            "orange",
            "apple"
        ],
        "edition": 3,
        "year": 2012
    }
]"
```
值得注意的序列化顺序：如果存在toJSON就调用该方法，否则进行数组过滤，函数过滤两种过滤器进行过滤，在使用第三个参数进行格式化

6、在解析JSON字符串时，如何把日期字符串还原Date对象？

```bash
// 定义一个带日期字符串的JSON对象
var objtest = [
  {"title": "animal", "authors": ["monkey","pig"], "edition": 3, "year": 2011, showDate: new Date(2019,10,22)},
  {"title": "fruit", "authors": ["orange","apple"], "edition": 3, "year": 2012, showDate: new Date(2019,10,22)}
  ];

// 通过JSON.stringify 格式化带日期格式的JSON字符串
var jsonStr = JSON.stringify(objtest, null, 4); 

"[
    {
        "title": "animal",
        "authors": [
            "monkey",
            "pig"
        ],
        "edition": 3,
        "year": 2011,
        "showDate": "2019-11-21T16:00:00.000Z"
    },
    {
        "title": "fruit",
        "authors": [
            "orange",
            "apple"
        ],
        "edition": 3,
        "year": 2012,
        "showDate": "2019-11-21T16:00:00.000Z"
    }
]"

// JSON.parse和JSON.stringify一样也有另一个函数参数
var result = JSON.parse(jsonStr,function(key,value) { 
	if(key == "showDate"){
		return new Date(value);
	}else{
		return value;
    }
});

// 解析出来的JSON对象，showDate 里面包含_proto_日期函数的原生
console.log(result.showDate.getFullYear()); // 2019

```
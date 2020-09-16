---
title: margin塌陷
date: 2020-03-10 11:30:33
tags:
  - 前端
  - css
  - 布局
---

>这涉及到浏览器对BFC约束,下一篇博文介绍BFC.简单的介绍一下margin塌陷和margin合并问题产生的原因及如何解决

#### margin塌陷

什么是margin塌陷？下面代码，我们给子元素添加margin当值小于100px你会发现没有任何变化，当大于100px你会发现父元素和子元素一起变动，父子元素的位置没有变动。这就是产生了margin塌陷，父元素相对于浏览器定位，而子元素没有相对于父元素定位。

``` bash
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>margin-塌陷</title>
<style>
     .parent{
         width:200px;
         height:200px;
         background-color:#0f0;
         margin-top:100px;
     }
     .child{
         width:50px;
	     height:50px;
         background-color:#00f;
         opacity:0.8;
     }
    </style>
</head>
<body >
	<div class="parent">
		<div class="child"></div>
	</div>
</body>

</html>

```

![概览](/assets/img/margin1.png)


如何解决margin塌陷？最直接的是给父元素增加broder，但不推荐。需要根据BFC约束，触发BFC(块级格式上下文),改变父级的渲染规则,如下四种触发BFC的方法，添加到parent中

(1)position:absolute/fixed

(2)display:inline-block;

(3)float:left/right

(4)overflow:hidden


#### margin合并

什么是margin合并？如下代码在浏览器运行后会发现，box1和box2之间的间隙不是250px而是150px，取最大的合并了

``` bash

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>margin-合并</title>
<style>
     .box1{
         width:100px;
         height:100px;
         background-color:#0f0;
         margin-bottom:100px;
     }
     .box2{
         width:100px;
	     height:100px;
         background-color:#00f;
		 margin-top:150px;
     }
    </style>
</head>
<body >
	<div class="box1"></div>
	<div class="box2"></div>
</body>

</html>

```

![概览](/assets/img/margin2.png)

如何解决margin合并问题？ 给box2包一层父元素，按照BFC原则设置overflow:hidden。或者给两个box都包一层父元素
如果不改变html结构，可以直接设置父元素的margin-bottom为250px

---
title: 轮播图
date: 2019-05-28 10:34:59
tags: bootstrap
---

>不同的前端UI里都有应用轮播图，最常用的是bootstrap的carousel，轮播图的一些常用用法总结

## 怎么使用？

### 要引入css和js，bootstrap.css 和  carousel.js

``` bash
<div id="carousel-example-generic" class="carousel slide" data-ride="carousel">
  <!-- Indicators -->
  <ol class="carousel-indicators">
    <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
    <li data-target="#carousel-example-generic" data-slide-to="1"></li>
    <li data-target="#carousel-example-generic" data-slide-to="2"></li>
  </ol>

  <!-- Wrapper for slides -->
  <div class="carousel-inner" role="listbox">
    <div class="item active">
      <img src="..." alt="...">
      <div class="carousel-caption">
        ...
      </div>
    </div>
    <div class="item">
      <img src="..." alt="...">
      <div class="carousel-caption">
        ...
      </div>
    </div>
    ...
  </div>

  <!-- Controls -->
  <a class="left carousel-control" href="#carousel-example-generic" role="button" data-slide="prev">
    <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
    <span class="sr-only">Previous</span>
  </a>
  <a class="right carousel-control" href="#carousel-example-generic" role="button" data-slide="next">
    <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
    <span class="sr-only">Next</span>
  </a>
</div>
```

## Carsoul的基本参数概览
![参数概览](../_images/carousel.png)

## Carsoul里面的常用属性说明
+ data-ride="carousel": k页面加载时加载轮播
+ data-interval="2000": 轮播时间
+ data-warp： 轮播是否循环，默认true
+ data-pause：鼠标覆盖循环暂停，默认true
+ class="carousel-indicators": 指定本内容小圆点
+ data-target="#carouselContainer"：指向父元素的id
+ data-slide-to="0": 指向内容索引(点击li时，跳转)
+ carousel-inner: 指定轮播内容
+ class="item": 轮播子页
+ class="carousel-caption": 轮播内容中提示信息
+ class="left carousel-control": 左边控制按钮
+ href="#carouselContainer": 指向父容器
+ class="glyphicon glyphicon-chevron-left": 左边图标


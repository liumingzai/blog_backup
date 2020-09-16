---
title: img的srcset和size属性是什么？
date: 2020-08-31 17:04:09
tags:
  - 前端
  - HTML
  - 响应式
---

>这是前端面试响应式布局及img标签会被扩展问到的一道题，紧随src和href重要性之后加分题。

#### 如何创建响应式图片？

# 使用image的srcset和sizes属性,根据屏幕密度实现对应尺寸图片

+ srcset: 定义了我们允许浏览器选择的图像集，以及每个图像的大小.
+ sizes: 定义一组媒体条件并指明当前某些媒体条件为真时，什么样的图片尺寸是追加选择
+ w：宽度规格，图像固有宽度，其与sizes属性设定的屏幕密度相关


``` bash

<img srcset="elva-fairy-320w.jpg 320w,
             elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
     sizes="(max-width: 320px) 280px,
            (max-width: 480px) 440px,
            800px"
     src="elva-fairy-800w.jpg" alt="Elva dressed as a fairy">

```

# 作为背景图片image-set()

``` bash

  .photo {
    background-image: -webkit-image-set("demo.png" 1x, "demo-2.png" 2x, "demo-3.png" 600dpi);
  }

```

demo链接

这里引入一个概念：什么是屏幕密度（Density）？及与其相关的几个属性
分辨率: 显示分辨率一定，显示屏越小图像越清晰，显示屏固定，分辨率越高越清晰。图像分辨率则是指图像单位英寸中所包含的像素点数
Density: 屏幕密度density表示每英寸有多少个现实点，与分辨率是两个概念一般3个常用固定值240/160/120
dip: device independent pixels(设备独立像素)与dp类似都是与密度无关的像素，和设备硬件有关，一种基于屏幕密度抽象单位。
dp：与密度无关的像素，在每英寸160点的显示器上，1dp=1px。设置长度、高度等属相可以用dp或者sp
sp：与密度无关的像素，放大像素主要用来处理字体大小，与scale无关
px: pixels(像素)屏幕上的点与密度相关，密度大了，单位面积上的px会比较多
pt：表示一个点，屏幕的一个屋里长度，1pt=1/72in
in：(英寸)屏幕的物理尺寸，屏幕对角线长度。每英寸等于2.54厘米
不同屏幕密度：
HVGA屏density=160
QVGA屏density=120
WVGA屏density=240
WQVGA屏density=120
计算公式： 
如果密度为320 密度因子320/160,如果设置的TextView宽度为160px，系统会自动将width设置为160px* 密度因子就是320px

[接下来补充一些解决方案来源于：]
(https://jakearchibald.com/2015/anatomy-of-responsive-images)
(https://www.zhangxinxu.com/wordpress/2015/11/anatomy-of-responsive-images/)


1、 固定图像尺寸，满足不同屏幕密度
![尺寸固定,密度变化](/assets/img/solution1.png) 

还有一些规则，没有在上图中涵盖：

内的每个项目srcset是<url> <density>x，例如cat-2x.jpg 2x。其中的项的顺序srcset没关系
如果不指定宽度/高度，浏览器将显示图像的原始宽度/高度除以密度。例如，如果选择了2x资源，它将以资源宽度/高度的50%呈现
这只是一个提示，即使在3x设备上，浏览器也可能使用1x图像，可能是由于连接不良


2、尺寸和密度都变化

![尺寸和密度都变化](/assets/img/solution2.png) 

不同宽度的图像通常作为响应网站内容的一部分使用。在这个博客中，内容图像占文章宽度的100%，但文章并不总是100%的窗口。

为了让浏览器选择正确的图像，它需要知道：

不同大小的图像的URL
每个图像资源的解码宽度
宽度<img>
最后一个是特别棘手的，因为图像在CSS准备好之前就开始下载了，所以<img>无法从页面布局中检测到。

理解这个语法的关键是要知道这些值中的哪个值是指窗口宽度、解码图像宽度和<img>宽度


3、尺寸，密度，资源都变化

![尺寸和密度都变化](/assets/img/solution3.png) 

与上一个示例类似，但是框架的宽度不同。这样你就可以以较小的宽度集中在主题上。

你可以有很多<source>随你的便
你呢必须包括<img>
媒体查询<source>永远都会被遵守，这不仅仅是一个暗示
媒体查询基于窗口的宽度，而不是<img>
第一次匹配<source>将被使用，所以顺序很重要
如果没有匹配<source>找到了<img>使用
这个<img>毕竟必须出现<source>s
<source>不支持src公司，但是srcset="whatever.jpg"同样有效
一旦<source>或<img>选定时srcset和尺寸属性的工作方式与前面的示例相同，因此您可以混合和匹配技术。

这个<picture>要素适用于Chrome、Firefox和Opera，然后回到<img>在其他浏览器中。我听说了可能会进入Edge的下一个版本，很不错


4、不同图片类别

![尺寸和密度都变化](/assets/img/solution4.png) 

类型是mime类型
你可以有多个来源和混合类型具有媒体 ,srcset，甚至尺寸制造可怕的东西
这个适用于Chrome、Firefox和Opera，然后回到<img>在其他浏览器中
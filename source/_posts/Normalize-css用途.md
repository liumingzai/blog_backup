---
title: Normalize.css用途
date: 2020-11-10 11:34:58
tags:
  - 前端
  - css
  - 布局
---


> Normalize.css是一个可定制的CSS文件, 是归一化的css文件, Normalize.css是现代化更小的css文件,在默认的HTML元素样式上提供了跨浏览器的高度一致性

+ [Normalize.css 项目地址](http://necolas.github.io/normalize.css/ "Normalize.css 项目地址")
+ [Normalize.css 在GitHub上的源码](https://github.com/necolas/normalize.css "Normalize.css 在GitHub上的源码")

### Normalize.css特性

1. 保留有用的默认值，不同于许多CSS的重置
2. 标注化的样式，适用范围广的元素
3. 纠正错误和常见的浏览器的不一致性
4. 一些细节的改进，提高了易用性
5. 使用详细的注解代码


### Normalize.css支持浏览器类型

+ Google Chrome (latest)
+ Mozilla Firefox (latest)
+ Mozilla Firefox ESR
+ Opera (latest)
+ Apple Safari 6+
+ Internet Explorer 8+


### Normalize.css与Reset.css 
 [转载https://jerryzou.com/posts/aboutNormalizeCss/](https://jerryzou.com/posts/aboutNormalizeCss/ "转载")

##### 一、Normalize.css 保护了有价值的默认值

  Reset通过为几乎所有的元素施加默认样式，强行使得元素有相同的视觉效果。相比之下，Normalize.css保持了许多默认的浏览器样式。这就意味着你不用再为所有公共的排版元素重新设置样式。当一个元素
在不同的浏览器中有不同的默认值时，Normalize.css会力求让这些样式保持一致并尽可能与现代标准相符合。

##### 二、Normalize.css 修复了浏览器的bug

它修复了常见的桌面端和移动端浏览器的bug。这往往超出了Reset所能做到的范畴。关于这一点，Normalize.css修复的问题包含了HTML5元素的显示设置、预格式化文字的font-size问题、在IE9中SVG的溢出、许多出现在各浏览器和操作系统中的与表单相关的bug。 例如：对于HTML5中新出现的input类型search，Normalize.css是如何保证跨浏览器的一致性的。

##### 三、Normalize.css 不会让你的调试工具变的杂乱

使用Reset最让人困扰的地方莫过于在浏览器调试工具中大段大段的继承链，如下图所示。在Normalize.css中就不会有这样的问题，因为在我们的准则中对多选择器的使用时非常谨慎的，我们仅会有目的地对目标元素设置样式。

##### 四、Normalize.css 是模块化的

  这个项目已经被拆分为多个相关却又独立的部分，这使得你能够很容易也很清楚地知道哪些元素被设置了特定的值。因此这能让你自己选择性地移除掉某些永远不会用到部分（比如表单的一般化）。

##### 五、 Normalize.css 拥有详细的文档

Normalize.css的代码基于详细而全面的跨浏览器研究与测试。这个文件中拥有详细的代码说明并在Github Wiki中有进一步的说明。这意味着你可以找到每一行代码具体完成了什么工作、为什么要写这句代码、 浏览器之间的差异，并且你可以更容易地进行自己的测试。



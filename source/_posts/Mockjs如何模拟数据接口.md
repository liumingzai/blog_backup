---
title: Mockjs如何模拟数据接口
date: 2019-12-25 13:59:04
tags:
  - Mockjs
  - Ajax
	- 模拟数据
---

> 本篇主要是介绍如何用mockjs生产后端模拟数据接口拦截Ajax,方便前后端分离,支持随机生成文本、数字、布尔值、日期、邮箱、图片等。


## 栏目
+ 安装
+ 实例
+ 使用
+ 总结

### 一、安装

[Mockjs官网](/http://mockjs.com/ "官网")

``` bash
 npm i mockjs --save 

 bower install mockjs --save

 yarn add morkjs

```

### 一、实例

[官网示例](/http://mockjs.com/examples.html/ "官网示例")

打开浏览器控制台console可以随按照示例联系


### 一、使用

项目中应用

定义mock.js

``` bash
import Mock from 'mockjs'

let Random = Mock.Random;
let ProductData = req => {
  let productList = [];
  for(let i=0; i<100; i++) {
    let product = {
      name: Random.ctitle(5,10),
      img: Random.dataImage('100x100', '茶缘'+Random.integer(1, 100)),
      price: Random.integer(1000, 10000),
      owner: Random.cname()
    }
    productList.push(product)
  }
  return productList;
};

Mock.mock('http://liumingzai.com.cn/getdata4', ProductData)

```
vue中引入
``` bash
import '@/mock/mock.js'

  export default {
    data(){
      return  {
        products: []
      },created() {
        axios.get(url4).then(res=>{
            this.products = res.data
        });
      }
    }
  }
```s

封装接口配置
``` bash

const MOCKURL = 'http://liumingzai.com.cn/';

const URL = {
  getData1: MOCKURL + 'getdata1',
  getData2: MOCKURL + 'getdata2',
  getData3: MOCKURL + 'getdata3',
  getData4: MOCKURL + 'getdata4'
};

export default URL;

```

页面设计

``` bash

   <div class="variety">
      <p>商品列表</p>
      <ul>
        <li class="variety-item" v-for="(item, index) in products" :key="index">
            <img :src="item.img" alt="">
            <p>{{item.name}}</p>
            <p>￥{{item.price}}</p>
        </li>
      </ul>
    </div>

```

效果
![预览](/assets/img/mock.png)



### 一、总结

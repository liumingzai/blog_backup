---
title: 微信浏览器内置WexinJSBridge对象
date: 2019-07-05 17:31:15
tags:
 - 微信公众号
 - 微信浏览器
 - WeixinJSBridge
---

### 微信浏览器内置JS对象WeixinJSBridge
> 微信公众号平台支持前端网页，可以分享朋友圈，关注微信，可以关闭公众号等


``` bash
// 分享朋友圈
function weixinShareTimeline(title, desc, link, imgUrl) {
    weixinJSBridge.invoke('shareTimeLine', {
    "img_url": imgUrl,
    "link": link,
    "desc": desc,
    "title": title
    });
}

// 发送好友
function weixinSendAppMessage(title, desc, link, imgUrl) {
    weixinJSBridge.invoke('sendAppMessage', {
        "img_url": imgUrl,
        "link": link,
        "desc": desc,
        "title": title
    });
}

// 分享到腾讯微博
function weixinShareWeibo(title, link) {
     weixinJSBridge.invoke('shareWeibo', {
        "content": title+link,
        "url": link,
    });
}

//关注微信号
function weixinAddContact(name) {
     weixinJSBridge.invoke('addContact', {webtype: '1', username: name}, function(e){
         WeixinJSBridge.log(e.err_msg);
         if(e.err_msg == 'add_contact:add' || e.err_msg =='add_contact:ok') {
             // add sucesss
         }
     });
}

//关闭安卓手机公众号
function closeWindow(){
    document.addEventListener('WeixinJSBridgeReady', function(){
        WeixinJSBridge.call('closeWindow');
    }, false);
}

// 关闭ios公众号
function closeWindow(){
    WeixinJSBridge.call('closeWindow');
}
```
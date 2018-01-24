---
title: Javascript-也许算是技巧的东西
date: 2017-08-12 20:28:54
tags: 
- JS
- 技巧整理
categories: 
- JS
- 技巧整理
thumbnail: 
---
在此主要整理一些常用或不常用的技巧，由于并不经常涉及`JavaScript`，所以可能有些并不是特别值得记录的东西。

####  1. var x = x || [];
变量`x`，如果它之前没有被定义过，那么它的值为[]。
在微信小程序的初始demo中有这样一段用法。

#### 2. 传递一个方法做形参
```js
getUserInfo:function(cb){
  var that = this
  if(this.globalData.userInfo){
    typeof cb == "function" && cb(this.globalData.userInfo)
  }else{
    //调用登录接口
    wx.login({
      success: function () {
        wx.getUserInfo({
          success: function (res) {
            that.globalData.userInfo = res.userInfo
            typeof cb == "function" && cb(that.globalData.userInfo)
          }
        })
      }
    })
  }
}
```
这里就是传递一个形参，cb的方法，并且这里还有一个很巧妙的判断方法
```js
typeof cb == "function" && cb(that.globalData.userInfo)
```
利用的`&&`的运算规律，首先判断`cb`是不是一个方法， 这里的`==`可以作为类型是否相当的判断，然后在`&&`中如果前面的不满足，后面的则不会执行；如果是`cb`是一个方法，调用`cb`方法，并且传入`success`成功回调的`userinfo`参数

*代码来源：微信小程序quickstart*


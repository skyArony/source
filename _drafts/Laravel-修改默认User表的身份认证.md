---
title: Laravel-修改默认User表的身份认证
tags:
  - null
categories:
  - null
date: 2018-05-31 19:18:51
thumbnail:
---

很多时候，我们需要对 Laravel 的登录进行更加深入的定制化，比如有种场景是这样的，用户要求系统的另一个第三方系统的认证，而那个第三方又没有开放 Oauth 2.0 相关授权接口。这样就需要用用户提供的账密模拟登录第三方系统，再对返回的结果进行包装再返回给用户。其实针对上述场景有两种解决方案：

- 保存用户的密码，用户登录时先从我们这进行一次鉴定，再提交到第三方进行一次鉴定：
  - 两次都成功登录成功
  - 其中任意一次成功，表明用户更改过密码，在第一次错误表明用户用新密码来进行登录了，更新数据库密码放行即可，在第二次错误表明，用更改密码后还是用旧密码等进行登录，提示但不放行
- 不保存用户密码，修改 Laravel 的认证部分用在第三方平台的返回结果来进行登录

这里主要讨论第二种方法在 Laravel 上如何实施。







> 编码格式utf8： /^[\x{4e00}-\x{9fa5}]+$/u 


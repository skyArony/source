---
title: 基于Lumen的API搭建（一）——权限控制部分
date: 2018-01-15 21:49:47
tags:
- Lumen
- PHP框架
- JWT
- Dingo
- API
categories: 
- 后端
- API
thumbnail: http://osv9x79o9.bkt.clouddn.com/18-1-15/13025253.jpg
---

承接上面四篇**Laravel-Lumen教程**，这里记录基于 `Lumen` 的 `Api` 开发流程。

# 一、Lumen + Dingo + JWT 基础配置
1. [Laravel/Lumen教程（一）——基础环境搭建](http://blog.yfree.cc/2017/12/30/Laravel-Lumen%E6%95%99%E7%A8%8B%EF%BC%88%E4%B8%80%EF%BC%89%E2%80%94%E2%80%94%E5%9F%BA%E7%A1%80%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/)
2. [Laravel/Lumen教程（二）——安装Laravel/Lumen并新建项目](http://blog.yfree.cc/2017/12/30/Laravel-Lumen%E6%95%99%E7%A8%8B%EF%BC%88%E4%BA%8C%EF%BC%89%E2%80%94%E2%80%94%E5%AE%89%E8%A3%85Laravel-Lumen%E5%B9%B6%E6%96%B0%E5%BB%BA%E9%A1%B9%E7%9B%AE/)
3. [Laravel/Lumen教程（三）——dingo的基本使用](http://blog.yfree.cc/2017/12/31/Laravel-Lumen%E6%95%99%E7%A8%8B%EF%BC%88%E4%B8%89%EF%BC%89%E2%80%94%E2%80%94dingo%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/)
4. [Laravel-Lumen教程（四）——JWT的基本使用](http://blog.yfree.cc/2018/01/11/Laravel-Lumen%E6%95%99%E7%A8%8B%EF%BC%88%E5%9B%9B%EF%BC%89%E2%80%94%E2%80%94JWT%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/)

# 二、设计要求
总的来说是要设计一个有极大自由度的 API 权限分配系统，具体要求如下：  
1. 每一条 `API` 作为一个 `item`；
2. 多个 `item` 可以组成一个 `group`；
3. 一个 `collection` 可以承载任意个 `item` 和 `group`，`collection` 分为长久和临时两种；
4. `user` 可以拥有某一个 `collection` 而拥有其中的 `item` 和 `group` 的权限；

# 三、数据库设计
先按上述要求把数据 E-R 图画出如下（部分字段省略）：

![ E-R 图](http://osv9x79o9.bkt.clouddn.com/18-1-16/68028673.jpg)

---
以后完善一下这个E-R图

```hell
php artisan make:migration create_api_items_table
php artisan make:migration create_api_groups_table
php artisan make:migration create_api_collections_table
php artisan make:migration create_group_items_table
php artisan make:migration create_collection_items_table
php artisan make:migration create_collection_groups_table
php artisan make:migration create_collection_users_table
```

# 四、其他

1. firstOrNew  massassignmentexception错误
需要设置fillable和guarded


2. 一个权限控制的中间件

3. 一个次数控制的中间件

4. 一个技术的后置中间件


写了写发现没什么好些的，就这样吧。
---
title: Laravel/Lumen教程2-安装以及项目新建
date: 2017-12-30 18:19:54
tags:
- Laravel
- Lumen
- PHP
categories: 
- 后端
- PHP
thumbnail: http://osv9x79o9.bkt.clouddn.com/17-12-30/62162735.jpg
---

这一篇主要介绍如何安装 Laravel 和 Lumen ，以及如何新建一个项目。



# 一、使用 Homestead

## Laravel

### 1. 安装 Laravel

```shell
composer global require "laravel/installer"
```

### 2. 新建项目

```shell
laravel new blog
# or
composer create-project --prefer-dist laravel/laravel blog
```



## Lumen

### 1. 安装 Lumen

```shell
composer global require "laravel/lumen-installer"
```

### 2. 新建项目

```shell
lumen new blog
composer install   # 如果到后面报错，后头执行这一句
cp .env.example .env
# or
composer create-project --prefer-dist laravel/lumen blog
```





# 二、不用 Homestead

## 1. composer 相关

在安装之前先确保 `.composer/vendor/bin` 目录已经在系统环境变量中，确保系统能够找到 Laravel 或 Lumen 的可执行文件。

### 找到对应目录

- Windows 系统的一般在“我的文档”的 `AppData\Roaming` 里；


- Linux 系统的一般在 `home/你的账户/` 里，如果是 deepin ，该目录下有个 `.config` 文件夹，一般在这里；

### 设置全局变量
Windows 系统不多说；

Linux 系统（deepin）如下：

```shell
cd /etc
sudo vim bash.bashrc

# 末尾加上
PATH="$PATH:/home/arony/.config/composer/vendor/bin"

# 保存后执行更新命令
source bash.bashrc
```



## 2. 安装和新建项目 

### Laravel

#### 1. 安装 Laravel

```shell
composer global require "laravel/installer"
```

#### 2. 新建项目

```shell
laravel new blog
# or
composer create-project --prefer-dist laravel/laravel blog
```

### Lumen

#### 1. 安装 Lumen

```shell
composer global require "laravel/lumen-installer"
```

#### 2. 新建项目

```shell
lumen new blog
composer install   # 如果到后面报错，后头执行这一句
cp .env.example .env
# or
composer create-project --prefer-dist laravel/lumen blog
```

新建项目后把 `storage` 目录的可写权限给web服务器。



# 三、其他配置

## 1. Lumen 相关

### 1.1 Lumen artisan 

Lumen 中精简了很多 artisan 命令。

可以安装 [flipbox/lumen-generator](https://packagist.org/packages/flipbox/lumen-generator) 增加 `lumen` 中缺失的 `Laravel` 中的 `aritisan` 命令。

### 1.2 Lumen helpers

Lumen 还精简了很多辅助函数，比如 auth 和 bcrypt 等。

可以安装 [cosmicvelocity/lumen-helpers](https://packagist.org/packages/cosmicvelocity/lumen-helpers) 或 [albertcht/lumen-helpers](https://packagist.org/packages/albertcht/lumen-helpers) 补全。

### 1.3 Lumen 其他一些配置 

打开 `bootstrap/app/php` ，把下面两行去掉注释，以开启 Facades 功能和 Eloquent 功能。

```
$app->withFacades();

$app->withEloquent();
```



## 2. 加密key

1. 执行 `php artisan key:generate` 生成 `.env` 文件中的加密 key。




## 3. 数据库

打开 `.env` 文件

```php
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=api_lumen  // 填入需要访问的数据库名
DB_USERNAME=root       // 账号
DB_PASSWORD=root       // 密码
```

### 3.1 多数据库配置
**.env**

```php
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=api_lumen  // 填入需要访问的数据库名
DB_DATABASE2=passport  // 填入需要访问的第二个数据库名
DB_USERNAME=root       // 账号
DB_PASSWORD=root       // 密码
```

**config/database.php**

```php
'mysql-passport' => [
    'driver' => 'mysql',
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', '3306'),
    'database' => env('DB_DATABASE2', 'passport'),
    'username' => env('DB_USERNAME', 'root'),
    'password' => env('DB_PASSWORD', 'root'),
    'unix_socket' => env('DB_SOCKET', ''),
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
    'strict' => true,
    'engine' => null,
],
```

**migrate**

```php
// users表，用户状态信息表
Schema::connection('mysql-passport')->create('users', function (Blueprint $table) {
    $table->increments('id');
    $table->timestamps();
});
```

**ORM模型操作数据库时**

```php
protected $connection = 'mysql-passport';  // 在该model中使用testdb2库
protected $table = 'article';
```

**DB Facade**

```php
DB::connection('mysql-passport')->select(...);
php
```



## 4. nginx (Homestead 下无需担心)

我这是 phpstudy 下的配置：

1. 打开 `phpstudy/nginx/conf/vhosts.conf` ;
2. 写入以下配置，配置多个时，写入多个即可，配置完记得重启 nginx

  ```
  server {
    listen       8001;      // 改成你想要的端口
    server_name  localhost;
    root   D:/phpStudy/WWW/lumen/api/public;   // 改成你的项目 public 目录
    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;  // 如果是403，多半是这个没加
    }
    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
    location ~ /.ht {
        deny  all;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }        
  }
  ```

### 4.1 测试是否可以访问

浏览器打开：http://127.0.0.1:8001



# 五、插一嘴关于 Lumen 的配置

## 默认配置
默认情况下, Lumen 使用单一的 .env 文件来配置你的应用, 然而, 你也可以使用 Laravel 风格 的配置方法.

只需要把 `vendor/laravel/lumen-framework/config` 文件夹下对应的配置文件复制到根目录下的 `config` 文件（没有的话自己创建）里面就行，并且例如 `JWT` 这样的库，也可以直接在 `vendor` 下找到这样的配置文件复制过去。

使用这种方法, 能有更多的配置灵活度。例如可以把其下的 `database.php` 复制过来，便于进行多数据库连接。

## 自定义配置文件

你可以创建自定义的配置文件, 并使用 `$app->configure()` 方法进行加载.

举个栗子, 把自定义配置文件放置于 `config/options.php`, 可以这样加载:

```php
// 在 bootstrap/app.php 中
$app->configure('options');
```


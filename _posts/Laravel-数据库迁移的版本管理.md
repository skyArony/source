---
title: Laravel-数据库迁移的版本管理
tags:
  - Laravel
categories:
  - Laravel
  - 数据库	
date: 2018-04-23 22:47:13
thumbnail:
---

Laravel 中的数据库迁移时用来管理数据库的，你可以把表结构写入到代码中，然后运行一条 artisan 命令既可以快速生成所需要的数据库，这些命令如何敲，官方文档里面已经很详细的介绍。

但是不可忽视的是：migrate 还可以用于数据库的版本控制，那这个版本控制体现在哪？如何进行版本控制？在官方文档中并没有比较详细的介绍，只是一笔带过，很多新手对此就会很迷惑。

这是本文讨论的核心就是：**数据库迁移的版本管理**。



# 一、迁移

## 1. 迁移的生成

在文档中，说下面的第一句可以用来生成数据表，而第二句的 `--create` 参数用来显示指定数据表的名称。

对于第三句的 `--table` 参数也是说指定数据表的名称。

**但其实第三句创建的这个迁移其实才是版本管理的核心。**

```shell
php artisan make:migration create_users_table

php artisan make:migration create_users_table --create=users

php artisan make:migration add_votes_to_users_table --table=users
```



## 2. down 和 up

```shell
php artisan migrate			    # 执行迁移命令
php artisan migrate:install		# 生成 migrations 表，记录迁移的执行情况
php artisan migrate:refresh		# 回滚之后再生成
php artisan migrate:reset		# 回滚所有迁移
php artisan migrate:rollback	# 回滚最近一次迁移，可以指定回滚次数
php artisan migrate:status		# 查看迁移的执行情况
```

你需要注意的是，执行迁移执行的是迁移文件中的 up 方法，而执行回滚执行的是迁移文件中的 down 方法，也就是说：

> 如果对于你自己新建的迁移文件，如果你执行回滚并没有发生任何变化，其实是因为你忘记编写 down 方法了。

以 Laravel 自带的迁移文件为例：

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->string('phone');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    public function down()
    {
        DB::statement('SET FOREIGN_KEY_CHECKS = 0');
        Schema::dropIfExists('users');
        DB::statement('SET FOREIGN_KEY_CHECKS = 1');
    }
}
```

up 方法生成数据库，而 down 会在执行回滚时被执行。

另外值得注意的是，这里还有一个小坑，如果你在别的迁移表中使用了外键约束，而外键来自于这个表，就会导致回滚失败，所以你需要添加下面这两行，把要 drop 表的命令夹在中间：

```php
Schema::disableForeignKeyConstraints();
Schema::dropIfExists('users');
Schema::enableForeignKeyConstraints();

// 如果是非默认数据库的
Schema::connection('mysql-passport')->disableForeignKeyConstraints();
```

>值得注意的是：执行回滚时，回滚会按照迁移执行的顺序逆向执行，而迁移执行的顺序是按照迁移文件的文件名前缀时间顺序执行的，例如：
>
>2014_10_12_100000_create_users_table 会在 2010_04_23_100442_add_votes_to_users_table 的背后执行。



## 3. 迁移的修改 

先安装一个插件，如果不安装，有些修改命令无法使用：

```shell
composer require doctrine/dbal
```

执行下面命令生成一个修改 user 表的迁移文件：

```shell
php artisan make:migration add_votes_to_users_table --table=users
```

生成后里面的内容是这样的：

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class AddVotesToUsersTable extends Migration
{
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            // 这是我加上去的，把字段 name 更名为 myname
            $table->renameColumn('name', 'myname');
            // 这是我加上去的，修改字段长度
            $table->string('item_key', 50)->change();
        });
    }

    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            //
        });
    }
}
```

> 值得注意的是，利用迁移修改字段属性时，只需加上需要修改的属性，而无需把以前生成时有的属性重复。



# 二、迁移的“一对多”

Laravel 中给的几个表，都是一个迁移文件对应一个表，一次修改对应一个表，但是这样如果表一多，整个文件夹就会很乱，所以我个人倾向于，把一个项目分成多个系统，然后每个系统的所有表放在一个迁移文件里面，把每次 的迁移修改放在一个迁移文件里。

## 1. 生成

在一个迁移文件中生成多个表：

```shell
php artisan make:migration create_api_sys
```

**2018_01_21_074602_create_api_sys.php**

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateApiSys extends Migration
{
    public function up()
    {
        Schema::create('api_items', function (Blueprint $table) {
            $table->increments('id');
            $table->string('item_key', 30)->unique()->comment('API唯一性key');
            $table->string('url', 150)->comment('API URL地址');
            $table->string('method', 10)->comment('API 调用方法');
            $table->string('intro', 500)->comment('API介绍');
            $table->timestamps();
        });

        Schema::create('api_groups', function (Blueprint $table) {
            $table->increments('id');
            $table->string('group_key', 30)->unique()->comment('group唯一性key');
            $table->string('intro', 500)->comment('Group介绍');
            $table->timestamps();
        });
    }

    public function down()
    {
        // 回滚用
        Schema::dropIfExists('api_items');
        Schema::dropIfExists('api_groups');
    }
}
```



## 2. 修改

在一个迁移文件中修改多个表：

```shell
php artisan make:migration change_2018_4_24_api_sys
```

**2018_04_24_113610_change_2018_4_24_api_sys.php**

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class Change2018426ApiSys extends Migration
{
    public function up()
    {
        Schema::table('password_resets', function (Blueprint $table) {
            $table->string('token')->comment('my comment')->change();
        });

        Schema::table('users', function (Blueprint $table) {
            $table->string('left_times')->comment('my comment')->change();
        });
    }

    public function down()
    {
        // 在这里写上面的反操作（能够让表变为之前状态的操作）
    }
}
```

> 这里有个坑：有着外键约束或唯一约束的表，在这里基本不能操作成功
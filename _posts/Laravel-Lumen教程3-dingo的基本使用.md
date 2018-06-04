---
title: Laravel/Lumen教程3-dingo的基本使用
date: 2017-12-31 23:59:18
tags:
- Laravel
- Lumen
- PHP
- Dingo
categories: 
- 后端
- PHP
thumbnail: http://osv9x79o9.bkt.clouddn.com/17-12-30/62162735.jpg
---



dingo 是 Laravel/Lumen 下一款便捷开发 Api 的组件，社区已经翻译了[文档](https://laravel-china.org/docs/dingo-api/2.0.0)，写的也很详细，我这就主要对社区的文档进行补充。然后还有自己的一点理解。



# 一、安装之前

打开以下两个文档，准备随时翻阅：
- [官方安装指导文档](https://github.com/dingo/api/wiki/Installation)
- [社区翻译文档](https://laravel-china.org/docs/dingo-api/2.0.0)



# 二、安装及基础配置

## 1. Facade

API 自带了两个 Facade，你可以酌情使用。

### `Dingo\Api\Facade\API`

这个是调度器的 Facade ，并提供了一些好用的辅助方法。

### `Dingo\Api\Facade\Route`

你可以使用这个 Facade 来获取 API 的当前路由、请求、检查当前路由的名称等。



## 2. .env

`.env` 文件的配置

```
API_STANDARDS_TREE=x            // x 本地或私有环境 prs 非商业销售的项目 vnd 公开的以及商业销售的项目
API_SUBTYPE=skyvote             // API简称
API_PREFIX=api              // API前缀，会体现在 URL 中 或使用API_DOMAIN - API子域名的形式，二选一
API_VERSION=v1                  // API默认版本
API_NAME="Sky Vote"             // API名称
API_CONDITIONAL_REQUEST=true    // api 缓存
API_STRICT=false                // 严格模式，要求客户端发送Accept头而不是默认在配置文件中指定的版本
API_DEFAULT_FORMAT=json         // 响应格式，默认的响应格式是JSON
API_DEBUG=true                  // 调试模式
```

如果你是用 Lumen 构建纯 API 项目，建议使用 `API_DOMAIN=api.domian.com`，如果是在 Laravel 项目中想写一些用于整个项目的 API ，建议使用 `API_PREFIX=api` ，否则你会发现一个神奇的事情：Laravel 中使用 dingo 后原生的路由使用不了了，提示 `The version given was unknown or has no registered routes.`

至于为什么，后面讲路由的时候再仔细说。





## 3. 错误格式

这里值得说道说道，Laravel 框架中框架本身和框架的扩展会有各种各样的错误返回，而这些返回的格式都不统一，但是 dingo 可以统一接管这些错误，并以固定的格式返回，只要那个错误是继承自 `Symfony\Component\HttpKernel\Exception\HttpException` 。

可以按下述修改，修改统一的错误返回格式：

**Lumen: bootstrap/app.php**

```php
$app['Dingo\Api\Exception\Handler']->setErrorFormat([
    'errcode' => -5000,
    'status' => ':status_code',
    'errmsg' => ':message',
    'errors' => ':errors',
    'data' => null,
    'debug' => ':debug'
]);
```

**Laravel: config/api.php**

```php
'errorFormat' => [
    'status' => ':status_code',
    'code' => -5000,
    'errmsg' => ':message',
    'errors' => ':errors',
    'data' => null,
    'debug' => ':debug',
],
```



## 4. 路由

看下面之前建议先看 [这篇文章](https://laravel-china.org/articles/4210/dingo-api-principle-from-the-perspective-of-laravel-http-request-processing?order_by=created_at&) 。

### Laravel

上面在 .env 的配置中说道，如果是 Laravel 项目，建议设置 `API_PREFIX` 而不是 `API_DOMAIN` 。

因为如果 Laravel 项目使用 `API_DOMAIN` ，在 **Dingo\Api\Http\Middleware\Request.php ** 这个定义的全局中间件中，`handle` 方法会根据一项值来检测，决定把路由交给框架还是交给 dingo，而这个值就是 `API_PREFIX` 和 `API_DOMAIN` 。

如果 Laravel 使用 `API_DOMAIN` 会导致所有的路由都交给了dingo 去处理，于是你就使用不了框架原生的路由了。但是如果 Laravel 使用 `API_PREFIX` ，恰好 Laravel 会把 **route\api.php** 中的路由都加上前缀 `api` ，就可以顺利的实现 api 相关路由 dingo 来管，而其他路由还是可以自由使用框架的原生路由写法。

**route\api.php**

```php
// 在 api 路由中不要用这种写法
//Route::group([
//
//    'prefix' => 'auth',
//
//], function ($router) {
//
//    Route::post('login', 'AuthController@login');
//    Route::post('logout', 'AuthController@logout');
//    Route::post('refresh', 'AuthController@refresh');
//    Route::post('me', 'AuthController@me');
//
//});

 $api = app('Dingo\Api\Routing\Router');
 $api->version('v1', ['namespace' => 'App\Http\Controllers'], function ($api) {
     // token 相关
     $api->group(['prefix' => 'auth'], function ($api) {
         $api->post('login', 'AuthController@login');
         $api->post('logout', 'AuthController@logout');
         $api->post('refresh', 'AuthController@refresh');
         $api->post('me', 'AuthController@me');
     });

     // activity 管理
     $api->post('activitys', 'ActivityController@store');
     $api->get('activitys', 'ActivityController@index');
     $api->get('activitys/{activity}', 'ActivityController@show');
     $api->match(['put','patch'] ,'activitys/{activity}', 'ActivityController@update');
     $api->delete('activitys/{activity}', 'ActivityController@destroy');

 });
```

**Dingo\Api\Http\Middleware\Request.php**

```php
public function handle($request, Closure $next)
{
    try {
        if ($this->validator->validateRequest($request)) {
            $this->app->singleton(LaravelExceptionHandler::class, function ($app) {
                return $app[ExceptionHandler::class];
            });

            $request = $this->app->make(RequestContract::class)->createFromIlluminate($request);

            $this->events->fire(new RequestWasMatched($request, $this->app));

            return $this->sendRequestThroughRouter($request);
        }
    } catch (Exception $exception) {
        $this->exception->report($exception);

        return $this->exception->handle($exception);
    }

    return $next($request);
}
```







## 1. 使用 composer 安装

打开 `composer.json` 文件,增加以下内容：
```php
// 我当时可用的版本是这个
"require": {"dingo/api": "2.0.0-alpha1"}
```
执行
```
composer update
```

## 2. 配置

根据框架决定后续

### Laravel
打开 `config/app.php` ,注册服务提供者：
```php
'providers' => [
    Dingo\Api\Provider\LaravelServiceProvider::class
]
```
如果你想进行额外的配置，执行以下命令即可发布一个配置文件（`api.php`）到 `config` 文件夹中（Lumen 中没有这个命令，当然你也可以安装这个命令，不过没必要）：
```
php artisan vendor:publish --provider="Dingo\Api\Provider\LaravelServiceProvider"
```

### Lumen
打开 `bootstrap/app.php`，注册服务提供者：
```php
$app->register(Dingo\Api\Provider\LumenServiceProvider::class);
```

### 配置
大部分配置可以在 `.env` 文件中配置好，一些微调需要在你发布的配置文件（Laravel）或者 `bootstrap/app.php`（Lumen）中配置。`.env`文件的配置如下：
```
API_STANDARDS_TREE=x
API_SUBTYPE=laravel-api
API_NAME=MyAPI
API_VERSION=v1
API_PREFIX=api    // 这个前缀会体现在 URL 中
API_CONDITIONAL_REQUEST=false
API_STRICT=false
API_DEFAULT_FORMAT=json
API_DEBUG=true
```

> API_STANDARDS_TREE - API规格 
>
  >> x 本地或私有环境  
  >> prs 非商业销售的项目  
  >> vnd 公开的以及商业销售的项目 
> 
> API_SUBTYPE - API简称  
> API_PREFIX - API前缀（或使用API_DOMAIN - API子域名）   
> API_VERSION - API默认版本  
> API_NAME - API名称  
> API_CONDITIONAL_REQUEST - 带条件的请求，由于缓存API请求的时候会使用客户端缓存功能，所以默认开启了带条件的请求  
> API_STRICT - 严格模式，要求客户端发送Accept头而不是默认在配置文件中指定的版本  
> API_DEFAULT_FORMAT - 响应格式，默认的响应格式是JSON  
> API_DEBUG - 调试模式



### Facades(这以后再写)
---



# 三、简单demo

## 1. 基本路由
### routes/web.php
```php
// dingo 使用自有的路由器，所以你需要先获取其实例
$api = app('Dingo\Api\Routing\Router');

// 必须定义一个版本分组，“v1”来自于 .env 文件，必须填写才生效
$api->version('v1', ['namespace' => 'App\Http\Controllers'], function ($api) {
  // 认证部分：获取token
  $api->get('/auth/token', 'AuthController@createToken');
  // 认证部分：刷新token
  $api->patch('/auth/token', 'AuthController@refreshToken');
  // 认证部分：删除token
  $api->delete('/auth/token', 'AuthController@deleteToken');

  // jwt-auth的路由保护，放在这里面的就需要带上token访问
  $api->group(['middleware' => 'auth:api'], function ($api) {
      // 资源获取：users
      $api->resource('users', 'UserController');
  });
});
```
访问 `127.0.0.1:8003/api/test` (8003 是我在 Nginx 中设置的端口)


## 2. 对返回数据进行统一格式化
有时候我们需要对从数据库取得的数据进行处理，然后才返回，这就需要用到 `transformer`，接下来简单介绍一下如何使用 `transformer`。

### 2.1 用 `migrate` 新建数据表，用 `seeder` 填充数据
首先我们要新建一个 `users` 表，然后填充几条测试数据，`Laravel` 中用 `数据迁移` 来进行表的创建和管理。

#### 新建数据表
`Laravel` 的 `database/migrations` 下有两个默认文件，执行以下命令即可生成两个默认表：
```
php artisan migrate
```

`Lumen` 的对应目录下默认没有那两个文件，可以从 `Laravel` 复制过去在用同样的命令生成。

#### 数据填充
在 `database/seeds` 中新建文件 `UsersTableSeeder.php`，写入：
```php
<?php
use Illuminate\Database\Seeder;

class UsersTableSeeder extends Seeder
{
  public function run()
  {
    DB::table('users')->insert([
      'name' => str_random(10),
      'email' => str_random(10).'@gmail.com',
      'password' => bcrypt('secret'),
      // Lumen 中没有 bcrypt() 这个函数，可以用下面一行代替
      // 'password' => app('hash')->make('secret'),
    ]);
  }
}
```

把 `database/seeds/DatabaseSeeder.php` 中
```php
// $this->call('UsersTableSeeder');
```
取消注释。

执行以下其中一句即可生成一条数据，执行三次，生成三条测试数据。
```
composer dump-autoload  // 如果以下运行不成功，先运行这个

php artisan db:seed  // 不指定的话，默认调用DatabaseSeeder

php artisan db:seed --class=UsersTableSeeder
```

### 2.2 新建 `Model` 绑定数据表，新建 `Controller` 取出数据

#### 新建模型绑定数据表
在 `app` 目录下，新建 `Models/DB/api_lumen2/User.php` （目录也是新建的，分这么多级目录是个人习惯），写入：
```php
<?php
namespace App\Models\DB\api_lumen2;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    protected $connection = 'mysql';  // 多数据库操作时最好进行绑定
    protected $table = 'users'; // 指定表
    protected $primaryKey = 'id'; // 指定主键
    public $timestamps = true;  // 是否自动维护时间戳
}
```

#### 新建控制器取出数据
在 `app/Http/Controllers` 中新建 `UserController.php`，写入：
```php
<?php
namespace App\Http\Controllers;

use App\Models\DB\api_lumen2\User;

class UserController extends Controller
{
  // 返回所有的user
  public function index()
  {
    $user = User::all();
    return $user;
  }

  // 按ID返回user
  public function show($id)
  {
    $user = User::findOrFail($id);  
    return $user;
  }
}
```

#### 设置路由
```php
$api->version('v1', function ($api) {
  $api->group(['namespace' => 'App\Http\Controllers'], function ($api) {
    $api->resource('users', 'UserController');  // 这里用到了资源路由，具体看文档
  }); 
});
```

访问 `http://127.0.0.1:8003/api/users` 和 `http://127.0.0.1:8003/api/users/1` 就可以看到返回的数据

**这样就成功实现了数据的取出操作，接下来介绍如何使用 `tansfomer` 来进行数据的格式化**

### 2.3 `Transfomer` 的使用

#### 使用方法0

一种官方文档没有记录的用法

```php
$res = (new UserTransformer)->transform($res);
```

#### 使用方法1

还不知道怎么用

```php
app('Dingo\Api\Transformer\Factory')->register('User', 'UserTransformer');
```

#### 使用方法2

##### 新建转换器

在 `app/Http/` 目录下新建 `Tansfomer` 文件夹，新建 `UserTransfomer.php`，写入：
```php
<?php
namespace App\Http\Transformers;

use App\Models\DB\api_lumen2\User;
use League\Fractal\TransformerAbstract;

class UserTransformer extends TransformerAbstract
{
  public function transform(User $user)
  {
    return [
        'id' => $user->id,
        'user_name' => $user->name,
        'user_email' => $user->email,
    ];
  }
}
```

##### 修改控制器
使用 `transfomer`，需要用到 `Dingo\Api\Routing\Helpers` 这个 `trait`，可以新建一个 `ApiController.php` 继承自基础控制器，在这个控制器中使用 `trait`，然后其他控制器继承这个控制器。

**ApiController**
```php
<?php
namespace App\Http\Controllers;

use Dingo\Api\Routing\Helpers;
use Laravel\Lumen\Routing\Controller as BaseController;

class ApiController extends BaseController
{
  use Helpers;
}
```

**UserController**
```php
<?php
namespace App\Http\Controllers;

use App\Models\DB\api_lumen2\User;
use App\Http\Transformers\UserTransformer;

class UserController extends ApiController
{
  // 返回所有的user
  public function index()
  {
    $user = User::all();
    return $this->response->collection($user, new UserTransformer);
  }

  // 按ID返回user
  public function show($id)
  {
    // 这里返回的数据类型是 collection，后面的响应大部分是要以 collection 来进行转换
    $user = User::findOrFail($id);  

    // 这里有诸多的响应方式，可以参考官方文档
    return $this->response->item($user, new UserTransformer);
  }
}
```

再访问 `http://127.0.0.1:8003/api/users` 和 `http://127.0.0.1:8003/api/users/1` 就可以看到格式化返回的数据

#### 自定义转换层
一直没弄懂怎么用，以后有时间具体在研究

---


## 3. 响应

### 3.1 对数据进行再包装
有时候，我们需要把需要的数据放在 `data` 中，然后 `data` 的同级，有一个 `code` 或 `errMsg`，要实现这种效果，我本来该想过进行两次 `tansfomer`，但是一直没成功，后来换了个思路，在 `ApiController` 中新建一个方法，对数据进行打包，用这种办法的话就不好用 `transfomer` 进行数据转换，因为 `transfomer` 直接返回了一个 `response` 对象，并不好再继续修改，所以必须自己实现数据转换。

**ApiController.php**
```php
<?php
namespace App\Http\Controllers;

use Dingo\Api\Routing\Helpers;
use Laravel\Lumen\Routing\Controller as BaseController;

class ApiController extends BaseController
{
    use Helpers;

    public $errcode;
    public $errmsg;
    public $status;
    public $header = '未封包';
    public $debug = '未调试';

    // 构造响应基本框架
    public function createResponse($data, $status, $errcode)
    {
        // 设置 errMsg
        $this->__setErrMsg($errcode);
        $body = array(
            'errcode' => $errcode,
            'status' => $status,
            'errmsg' => $this->errmsg,
            'header' => $this->header,
            'debug' => $this->debug,
            'data' => $data
        );
        // 如果对返回数据有更多设置需求，安装文档指导改这里即可。
        return $this->response->array($body)->setStatusCode($status);
    }
    
    // 设置debug内容，个人习惯，可以无视
    public function setDebug($debug)
    {
        $this->debug = $debug;
    }

    // 当 envelope=true 时将，将 header 头插入返回体中，个人想要实现一个特效，可以无视
    public function setHeader($header)
    {
        $this->header = $header;
    }

    // 设置错误码和错误内容
    public function __setErrMsg($errcode)
    {
        $msgForCode = array(
            0 => '成功',
            1 => '成功(未修改密码)',
            2 => '成功(未绑定邮箱) 请前往教务系统绑定邮箱 ：http://jwxt.***.edu.cn/jsxsd/',
            3 => '删除了不存在的数据',
            -1 => '网络故障',
            -2 => '未知错误',
            -3 => '验证码错误',
            -4 => '用户名或密码错误',
            -5 => 'HTTP错误',
            -6 => '唯一性限制',
            -7 => '更新不存在的数据，导致失败',
            -8 => '数据不存在',
            -9 => '越权限操作',
            -10 => '账户可调用次数不足',
            -65535 => '参数错误'
        );
        $this->errmsg = $msgForCode[$errcode];
    }
}
```

**UserController.php**
```php
// 返回所有的user
public function index()
{
  $user = User::all();
  $userArray = $user->toArray();
  $userData = array();

  foreach ($userArray as $key => $value) {
    $array = array(
      'id' => $value['id'],
      'user_name' => $value['name'],
      'user_email' => $value['email']
    );
    $userData[] = $array;
  }
  $data = json_encode($userData);

  return $this->createResponse($data, 201, 0);
}
```

**最后返回的数据**
```json
{
  "errcode": 0,
  "status": 201,
  "errmsg": "成功",
  "header": "未封包",
  "debug": "未调试",
  "data": "数据略..."
}
```


### 3.2 修改默认的错误返回格式
在上面，我返回的数据格式是我自定义的，但是有时候返回一些错误的时候，其格式和我定义的格式并不相符，作为接口，当然最后是错误和正确的返回格式一致，所以就需要对错误的返回格式进行修改。

**错误示例：**
```php
return $this->response->errorNotFound();
```
```json
{
  "message": "Not Found",
  "status_code": 404
}
```

**设置方法：**
在 `bootstrap/app.php` 中配置：
```php
$app['Dingo\Api\Exception\Handler']->setErrorFormat([
    'errcode' => -5,
    'status' => ':status_code',
    'errmsg' => ':message',
    'data' => null,
    'header' => "未封包",
    'debug' => ':debug'
]);
```
注意放在 `$app->register(Dingo\Api\Provider\LumenServiceProvider::class);` 之后。

这样错误返回为：
```json
{
  "errcode": -5,  // 这里-5固定为HTTP错误
  "status": 404,
  "errmsg": "NOT FOUND",
  "data": null,
  "header": "未封包",
  "debug": "数据略..."
}
```

## 4. 结合认证

`dingo` 支持三种认证方式

> HTTP Basic (Dingo\Api\Auth\Provider\Basic)  
> JSON Web Tokens (Dingo\Api\Auth\Provider\JWT)  
> OAuth 2.0 (Dingo\Api\Auth\Provider\OAuth2)

因为我个人主要使用 `JWT` 方式，所以接下来另开几篇文章来介绍如何使用 `JWT`。


## 5. Response 总结
```php
// dingo：controller 中
return $this->response->error('This is an error.', 404);
return $this->response->errorNotFound();

// laravel/lumen 自带的响应
return response('Unauthorized.', 401);
return response()->json(['errmsg' => 'Unauthorized'], 401);
```

## 6. Lumen 框架下的路由小坑
假设路由是这样的：
```php
$api->get('/edu/grade/{term}', 'EduGradeController@getGrade');
```
本来 `Laravel` 框架下，获取路由路径中的参数可以用以下两种方式：
```php
$term = $request->term;

$term = $request->route('term');
```
但是 `Lumen` 都不可以，你必须自己解析 `$request->route()`:
```php
// 定义这个方法
function route_parameter($name, $default = null)
{
    $routeInfo = app('request')->route();

    return array_get($routeInfo[2], $name, $default);
}

// 打印你的参数
dd($this->route_parameter('term'));
```


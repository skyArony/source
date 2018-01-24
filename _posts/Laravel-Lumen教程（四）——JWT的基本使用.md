---
title: Laravel-Lumen教程（四）——JWT的基本使用
date: 2018-01-11 18:15:57
tags:
- Laravel
- Lumen
- PHP框架
- JWT
categories: 
- 后端
- PHP
- 框架
thumbnail: http://osv9x79o9.bkt.clouddn.com/18-1-12/98107758.jpg
---
　`主题有bug，标记`
**JWT** 全称 **JSON Web Tokens** ，是一个非常轻巧的规范。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。它的两大使用场景是：认证和数据交换。

# 一、安装之前
先摆出几个参考资料，可以把连接都打开，方便查阅：
- [项目Wiki](https://github.com/tymondesigns/jwt-auth/wiki/Installation)
- [公众号coding01](https://mp.weixin.qq.com/s/KVUQE2DUetNB2kqxHs0VDg)，[JWT安装及简单例子](https://juejin.im/post/5a0812a16fb9a0451704ad96)
- [官方安装指导文档](http://jwt-auth.readthedocs.io/en/docs/laravel-installation/)
- [JWT的介绍](https://jwt.io/introduction/)

# 二、安装及基础配置

## 1. 使用 composer 安装

**命令行安装**

执行
```
// 建议使用1.0以上版本
composer require tymon/jwt-auth 1.*@rc
```

**配置文件安装**

打开 `composer.json` 文件,增加以下内容：
```php
// 我当时可用的版本是这个
"require": {"dingo/api":"1.*@rc"}
```
执行
```
composer update
```

## 2. 修改配置文件

### 2.1 取消一些行的注释
#### Lumen ———— bootstrap/app.php 
把以下的注释取消
```php
// 如果你是安装教程一步一步来的，这一行应该前面已经取消了
$app->withFacades();

// 增加 auth 中间件
$app->routeMiddleware([
    'auth' => App\Http\Middleware\Authenticate::class,
]);

// 认证所需
$app->register(App\Providers\AuthServiceProvider::class);
```

### 2.2 添加服务提供者
#### Lumen
**bootstrap/app.php** 文件中，在服务提供者位置添加
```php
// 有些文档里是说添加 Tymon\JWTAuth\Providers\JWTAuthServiceProvider::class ，可能是以前版本的，我也不是很清楚
$app->register(\Tymon\JWTAuth\Providers\LumenServiceProvider::class);
```

#### Laravel
**config/app.php** 中 添加：
```php
'providers' => [
    ...
    Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
]
```

### 2.3 发布配置文件到config文件夹

#### Laravel
```
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```
成功后config文件夹中会多一个 `jwt.php` 配置文件

#### Lumen
**Lumen** 中没有 `publish` 命令，直接在 `Vendor/tymon/jwt-auth/config` 目录找到文件 `config.php` 复制到 `\config` 下并重命名为 `jwt.php` 即可。

#### auth.php
除此之外，把 `\vendor\laravel\lumen-framework\config\auth.php` 也复制到 `config` 文件夹

### 2.4 配置jwt_secret
```
php artisan jwt:secret
```
执行后会在 `.env` 文件生成 `JWT_SECRET`

### 2.5 更新你的User模型
```php
// 引入这个
use Tymon\JWTAuth\Contracts\JWTSubject;

// 添加到 implements 后
class User extends Model implements AuthenticatableContract, AuthorizableContract, JWTSubject

// 添加以下方法
/**
 * Get the identifier that will be stored in the subject claim of the JWT.
 *
 * @return mixed
  */
public function getJWTIdentifier()
{
    return $this->getKey();
}

/**
 * Return a key value array, containing any custom claims to be added to the JWT.
 *
 * @return array
  */
public function getJWTCustomClaims()
{
    return [];
}
```

### 2.6 配置 auth.php
在这里，我们告诉 `api` guard 使用 `jwt` 驱动程序，我们将 `api` guard 设置为默认值。
```php
'defaults' => [
    'guard' => 'api',
    'passwords' => 'users',
],

...

'guards' => [
    'api' => [
        'driver' => 'jwt',
        'provider' => 'users',
    ],
],

...

'providers' => [
    'users' => [
        'driver' => 'eloquent',
        'model' => \App\User::class
    ]
],
```

# 三、简单demo

## 1. 新建 `AuthController`
```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use App\Http\Controllers\Controller;
use Tymon\JWTAuth\Facades\JWTAuth;

class AuthController extends Controller
{
    /**
     * 添加一个认证中间件，因为获取token无需认证，所以把createToken除外
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth:api', ['except' => ['createToken', 'refreshToken']]);
    }

    /**
     * 创建并获取一个token，要求附带email和password（数据来源users表）
     *
     * @param  \Illuminate\Http\Request  $request
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function createToken(Request $request)
    {
        $credentials = $request->only('email', 'password');

        if ($token = JWTAuth::attempt($credentials)) {
            return $this->respondWithToken($token);
        }

        return response()->json(['error' => 'Unauthorized'], 401);
    }

    /**
     * 注销，把所给token加入黑名单
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function deleteToken()
    {
        Auth::logout();

        return response()->json(['message' => 'Successfully logged out']);
    }

    /**
     * 刷新token，如果开启黑名单，以前的token便会失效，指的注意的是用上面的getToken再获取一次Token并不算做刷新，两次获得的Token是并行的，即两个都可用。
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function refreshToken()
    {
        return $this->respondWithToken(JWTAuth::parseToken()->refresh());
    }

    /**
     * 将返回结果包装
     *
     * @param  string $token
     *
     * @return \Illuminate\Http\JsonResponse
     */
    protected function respondWithToken($token)
    {
        return response()->json([
            'access_token' => $token,
            'token_type' => 'bearer',
            'expires_in' => JWTAuth::factory()->getTTL() * 60
        ]);
    }

    /**
     * 获取当前token的鉴权用户
     *
     * @return \Illuminate\Http\JsonResponse
     */
    public function me()
    {
        return response()->json(JWTAuth::user());
    }

    /**
     * Get the guard to be used during authentication.
     *
     * @return \Illuminate\Contracts\Auth\Guard
     */
    public function guard()
    {
        return JWTAuth::guard();
    }
}
```

## 2. 设定路由
此处用了 `dingo` 的路由，`UserController` 的内容参考以前的教程
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

### 2.1 一个值得注意的地方
> 这里有一个十分令人疑惑的地方： `'middleware' => 'auth:api'`，你会发现把 `:api` 去掉也是没问题的，到底怎么回事？
 
去文档看，这种 `路由名:参数` 的写法是用来传参的。但是这里并不是传参的，打开 `config/auth.php`：
```php
'guards' => [
    'api' => [
        'driver' => 'jwt',
        'provider' => 'users',
    ],
    'web' => []
],
```
仔细想了想，终于理解了：在 `Laravel` 中有 `web.php`、`api.php` 等多个路由，这个几个路由会分别对应这 `guards` 中一个数组，而 `JWT` 用 `auth` 中间件，后面的 `:api` 就表示用 `guards` 中 `api` 数组指定的 `driver` 和 `provider`，当只有一个 `api` 数组的时候，就可以省去 `:api`。

## 3. `Token` 的获取、使用、删除和刷新
以下用 `postman` 演示：

### 3.1 获取一个 `token` 
![GET 方式获取一个 token](http://osv9x79o9.bkt.clouddn.com/18-1-12/40738984.jpg)

### 3.2 带上 `token` 访问受保护的资源

#### 不带 token 的错误返回
这时候如果不带上 `token` 去访问受保护的资源会返回 `Unauthorized.` 在`\app\Http\Middleware\Authenticate.php 39行` 设置。

#### 附带 token 进行访问的两种方式

**1. 加到 url 中**  
`?token=你的token`

![用 token 获取数据-URL方式](http://osv9x79o9.bkt.clouddn.com/18-1-12/34542301.jpg)

**2. 加到 header 中**  
在 `header` 中 添加 `Authorization` ，值为 `Bearer 你的token` 注意中间有个空格。

![用 token 获取数据-header方式](http://osv9x79o9.bkt.clouddn.com/18-1-12/69293149.jpg)

### 3.3 删除一个 token
![DELETE 方式删除一个 token](http://osv9x79o9.bkt.clouddn.com/18-1-12/52986944.jpg)

删除 `token` 后，token就会失效，无法再利用其获取数据。

### 3.4 刷新你的 token

![PATCH 方式刷新token](http://osv9x79o9.bkt.clouddn.com/18-1-12/25126227.jpg)

刷新后，旧 `token` 将会失效,但是你可以设置一个宽限时间，这个在后面具体说。

## 4. `token` 的组成、创建以及解析

### 4.1 `token` 的组成
一个JWT实际上就是一个字符串，它由三部分组成，头部、载荷与签名，中间用 `.` 分隔，例如：`xxxxx.yyyyy.zzzzz`

#### 头部（header）
头部通常由两部分组成：令牌的类型（即JWT）和正在使用的散列算法（如HMAC SHA256 或 RSA.）。
例如：
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
然后用 `Base64Url` 编码得到头部，即 `xxxxx`。


#### 载荷（Payload）
载荷中放置了 `token` 的一些基本信息，以帮助接受它的服务器来理解这个 `token`，载荷的属性也分三类：预定义（Registered）、公有（public）和私有（private），接下来主要介绍预定义的。
```json
{
  "sub": "1",
  "iss": "http://localhost:8000/auth/login",
  "iat": 1451888119,
  "exp": 1454516119,
  "nbf": 1451888119,
  "jti": "37c107e4609ddbcc9c096ea5ee76c667"
}
```
这里面的前6个字段都是由JWT的[标准](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)所定义的，也就是预定义（Registered claims）的。

> - sub: 该JWT所面向的用户
> - iss: 该JWT的签发者
> - iat(issued at): 在什么时候签发的token
> - exp(expires): token什么时候过期
> - nbf(not before)：token在此时间之前不能被接收处理
> - jti：JWT ID为web token提供唯一标识

将上面的 `json` 进行 `Base64Url` 编码得到载荷，，即 `yyyyy`。


#### 签名（Signature）
签名时需要用到前面编码过的两个字符串，如果以 `HMACSHA256` 加密，就如下：
```php
HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secret
)
```
最后得到的字符串就是 `token` 的第三部分 `zzzzz`。

组合便可以得到 `token：xxxxx.yyyyy.zzzzz`。

这里插一嘴签名的作用：保证 JWT 没有被篡改过，原理如下：
> HMAC 算法是不可逆算法，类似 MD5 和 hash ，但多一个密钥，密钥（即上面的secret）由客户端和服务端共享，服务端把 token 发给客户端后，客户端可以把其中的头部和载荷再加上事先共享的 secret 再进行一次 HMAC 加密，得到的结果和 token 的第三段进行对比，如果一样则表明数据没有被篡改。


### 4.2 `token` 的创建
前面的 `AuthController.php` 中有两行展现了这一种 `token` 的创建方法，即用用户所给的账号和密码进行**尝试**，密码正确则用对应的 `User` 信息返回一个 `token` 。
```php
// 用 request 中的 email 和 password ，验证后返回token
$credentials = $request->only('email', 'password');
$token = JWTAuth::attempt($credentials);
```
但 `token` 的创建方法不止这一种，接下来介绍 `token` 的三种创建方法。

#### a) 基于请求中的参数
这就是刚刚说的哪一种，贴出具体代码。
```php
use Illuminate\Support\Facades\Auth;
use Tymon\JWTAuth\Facades\JWTAuth;
use Tymon\JWTAuth\Exceptions\JWTException;

class AuthenticateController extends Controller
{
    public function authenticate(Request $request)
    {
        // grab credentials from the request
        $credentials = $request->only('email', 'password');     // 获取请求中的 email 和 password 值

        try {
            // attempt to verify the credentials and create a token for the user
            if (! $token = JWTAuth::attempt($credentials)) {
                return response()->json(['error' => 'invalid_credentials'], 401);
            }
        } catch (JWTException $e) {
            // something went wrong whilst attempting to encode the token
            return response()->json(['error' => 'could_not_create_token'], 500);
        }

        // all good so return the token
        return response()->json(compact('token'));
    }
}
```

#### b) 基于users表中的用户信息 + 数组
基于用户信息，使用 `fromeUser` 方法。
```php
$user = User::first();

$token = JWTAuth::fromUser($user);
```
基于用户信息 + 自定义的数组信息
```php
$customClaims = ['foo' => 'bar', 'baz' => 'bob'];

// jwt-0.5
JWTAuth::attempt($credentials, $customClaims);  //对应可以用JWTAuth::authenticate()取回$user
// or
JWTAuth::fromUser($user, $customClaims);  // 对应的可以用JWTAuth::toUser($token)取回$user

// jwt-1.0
$customClaims = ['from' => 'sky31 Development', 'to' => 'sky31 developer'];
$token = JWTAuth::customClaims($customClaims)->attempt($credentials) // 用JWTAuth::parseToken()->getPayload() 获取载荷，载荷中包括你刚刚插入的 customClaims 信息
$token = JWTAuth::customClaims($customClaims)->fromUser($user)

```
数组信息会在 `token` 解码时得到，同时越大的数组会生成越长的 `token` ，所以不建议放太多的数据。同时因为载荷是用 `Base64Url` 编码，所以相当于明文，因此绝对不能放密码等敏感信息。


#### c) 基于任何你喜欢的东西，为更高级的定制提供了可能（这是从文档直接复制的，没做具体实验，以后用到这再更新）
您可以将声明直接链接到 `Tymon\JWTAuth\PayloadFactory` 实例（或使用 `JWTFactory Facade` ）
```php
$customClaims = ['foo' => 'bar', 'baz' => 'bob'];
$payload = JWTFactory::make($customClaims);
$token = JWTAuth::encode($payload);
```
```php
// add a custom claim with a key of `foo` and a value of ['bar' => 'baz']
$payload = JWTFactory::sub(123)->aud('foo')->foo(['bar' => 'baz'])->make();

$token = JWTAuth::encode($payload);
```

### 4.3 `token` 的解析
#### a) 把 `token` 解析到对象，会返回一个对象
```php
// this will set the token on the object
JWTAuth::parseToken();
```
#### b) 获取到原先嵌入到 `token` 的 `user` 信息
```php
// and you can continue to chain methods
$user = JWTAuth::parseToken()->authenticate();
// or
$user = JWTAuth::parseToken()->toUser();
```
#### c) 获取 `token` ，如果 `token` 被设置则会返回，否则会尝试使用方法从请求中解析 `token` ，如果token未被设置或不能解析最终返回false。
```php
$token = JWTAuth::parseToken()->getToken();
```
#### d) 甚至可以设置 `token` 。
```php
JWTAuth::setToken('foo.bar.baz');
```

#### e) 获取载荷
`checkOrFail()` 或 `getPayload()` 或 `payload()`
```php
// 获取所有载荷
$payload = JWTAuth::parseToken()->getPayload()

// 获取载荷属性
JWTAuth::getClaim('exp')
```

#### f) 如果是前端
直接 `base64` 解码 `token` 的前两段即可以知道所需的信息。

## 5. 解释一下 `JWTAuth::` 和 `Auth::`
> 在此解释一下为什么都是用的 `JWTAuth::` ，而在网上看到的还有 `Auth::` 和 `$this->guard()`？
>
> 查看源码，会发现 `JWTAuth` 通过 `Facade` 方式从而可以静态访问 `JWTAuth` 的方法，而 `JWTAuth` 又继承自 `JWT` ，`JWT` 中使用了 `CustomClaims` 这个 `trait` 查看这两个类可以查看方法的具体实现。

### 5.1 区别
#### 刷新 `token`
```php
Auth::refresh();

JWTAuth::parseToken()->refresh();
```

#### 注销 `token`
```php
Auth::logout();

JWTAuth::parseToken()->invalidate();  // invalidate 还有部分参数可选
```

#### 设置 `custom claims`
```php
// 只发现 JWTAuth:: 可以做到
$customClaims = ['xm' => 'yf', 'sex' => 'male'];
$token = JWTAuth::customClaims($customClaims)->attempt($credentials)
```


## 6. `Token` 的相关属性设置

### 6.1 有效时间、刷新时间、宽限时间
一个 `token` 一般来说有三个时间属性，其配置都在 **config/jwt.php** 内。

#### 有效时间
有效时间指的的是你获得 `token` 后，在多少时间内可以凭这个 `token` 去获取内容，逾时无效。
```php
// 单位：分钟
'ttl' => env('JWT_TTL', 60)
```

#### 刷新时间
刷新时间指的是在这个时间内可以凭旧 `token` 换取一个新 `token`。例如 `token` 有效时间为 60 分钟，刷新时间为 20160 分钟，在 60 分钟内可以通过这个 `token` 获取新 `token`，但是超过 60 分钟是不可以的，然后你可以一直循环获取，直到总时间超过 20160 分钟，不能再获取。
```php
// 单位：分钟
'refresh_ttl' => env('JWT_REFRESH_TTL', 20160)
```

#### 宽限时间
宽限时间是为了解决并发请求的问题，假如宽限时间为 0s ，那么在新旧 `token` 交接的时候，并发请求就会出错，所以需要设定一个宽限时间，在宽限时间内，旧 `token` 仍然能够正常使用。

```php
// 宽限时间需要开启黑名单（默认是开启的），黑名单保证过期token不可再用，最好打开
'blacklist_enabled' => env('JWT_BLACKLIST_ENABLED', true)

// 设定宽限时间，单位：秒
'blacklist_grace_period' => env('JWT_BLACKLIST_GRACE_PERIOD', 60)
```


## 7. 关于 `JWT` 的讨论
### 7.1 为什么用 `JWT`？
首先因为 `HTTP` 是无状态的协议，每一个请求都不会受到前后请求的状态影响，所以就需要 **维护和验证登录状态** 。

**传统方法**：`cookies` 和 `session`  

**只用cookies**：由于用于验证的敏感信息都保存在本地，即使进行了加密，也并不是特别安全。  

**session**：需要在 `cookies` 中保存 `sessionID` ，然后在服务器中保存 `session` ，其中包括会话ID和用户ID ，同时由于 `session` 常常保存在内存中，所以会对服务器造成比较大的压力。

再加上 `cookies` 的不可跨域名性（子域名之间也算跨域名），跨域操作也存在问题。

**JWT**：
使用 `JWT` 可以做到前后端脱离，解决跨域问题，消除服务器存储session的压力，但是同时增加了计算压力。这两者怎么选择，开发者可以自行考虑。

### 7.2 `token` 的刷新问题？
#### `token` 为什么要刷新吗？
首先 `Basic Auth` 是一种最简单的认证方法，但是由于每次请求都带用户名和密码，频繁的传输肯定不安全，所以才有 `cookies` 和 `session` 的运用。如果 `token` 不刷新，那么 `token` 就相当于上面的用户名+密码，只要获取到了，就可以一直盗用，因此 `token` 设置有效期并能够进行刷新是必要的。

#### a) `token` 有效期多久合适，刷新频率多久合适？
有效期越长，风险性越高，有效性越短，刷新频率越高，刷新就会存在刷新开销，所以这需要综合考虑。我个人一般考虑的范围是：15min ~ 120min。

#### b) 有没有必要每次都刷新 `token` ？
上面考虑的 15min ~ 120min，会存在一个问题，就是**重放攻击**风险，防御这个风险，在 `JWT` 可用的方案是每次请求后都刷新一次 `token` ，但这样又会存在一个新的问题：并发请求。一次并发请求是用的一个 `token` ，第一个完成的请求会导致后面的请求全部失败。可用的解决方案是设置**宽限时间**，即一个 `token` 刷新后，旧 `token` 仍然短暂的可用。可惜这样并不能完美的解决重放攻击，只是增大了不法者攻击的成本。这个问题在 `JWT` 中并没有很好的解决。
```php
// 每次刷新的话需要用到第二个中间件
// 第一个中间件和前面保护的路由的 auth 相同（猜的，我也不确定）
protected $routeMiddleware = [
	...
	'jwt.auth' => 'Tymon\JWTAuth\Middleware\GetUserFromToken',
	'jwt.refresh' => 'Tymon\JWTAuth\Middleware\RefreshToken',
];
```

#### `token` 的刷新总结
因为无法完全解决重放攻击，所以在因重放攻击会导致巨大安全问题和损失的地方，建议使用其他安全认证措施。而日常 `Api` 使用建议如下设置：  
```
有效时间：15min ~ 120min
刷新时间：7天 ~ 30天
宽限时间：60s
```

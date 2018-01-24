---
title: 针对本工作室业务的 RESRful API 设计
date: 2017-10-27 10:49:50
tags: 
- 后端
- API
- 开发记录
categories: 
- 后端
- API
thumbnail: 
---
# 一、RESTful是什么
RESTful API是一种设计概念，网上有很多关于它的解释，但我最喜欢的一个解释是：

> 看Url就知道要什么  
> 看http method就知道干什么  
> 看http status code就知道结果如何  
>  
> ——知乎 徐磊

好概念介绍完毕。

四大特点：`资源`、`统一接口`、`URI`和`无状态`。
好,概念很简单就介绍完毕了。接下来，列举网上找到的参考资料，然后通过这些资料整理一个适合工作室的RESTful设计规范，并以此规范完成工作室`API2.0`。

# 二、参考资料
- GitHub上一个项目：[RESTful API 设计参考文献列表](https://github.com/aisuhua/restful-api-design-references)
- [RESTful API 编写指南](https://blog.igevin.info/posts/restful-api-get-started-to-write/)
- [知乎-关于 RESTful API 中 HTTP 状态码的定义的疑问？-Liril](https://www.zhihu.com/question/58686782)
- [阮一峰老师的跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
- [RESTful 架构风格概述 - Gevin](https://blog.igevin.info/posts/restful-architecture-in-general/)

# 三、RESTful设计
## 1. 协议
必须https。

## 2. 单独的域名
```
https://api.example.com
```

## 3. HTTP动词
对于资源的具体操作类型，由HTTP动词表示。
常用的HTTP动词有下面五个（括号里是对应的SQL命令）。
```
GET（SELECT）：从服务器取出资源（一项或多项）。
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
DELETE（DELETE）：从服务器删除资源。
```
下面是一些例子
```
GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/ID：获取某个指定动物园的信息
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```
值得注意的是PUT和PATCH的区别：

- 更新一个资源的全部信息时采用`PUT`
- 更新一个资源的部分信息时采用`PATCH`

## 4. 版本化
将API的版本号放进URL中：
```
https://api.example.com/v1/
```

## 5. 路径
路径又称"终点"（endpoint），表示API的具体网址。
在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有**名词**，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用**复数**。
举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样：
```
https://api.example.com/v1/zoos
https://api.example.com/v1/animals
https://api.example.com/v1/employees
```

## 6. 分析&限制
对整个API系统必须有一套完整的跟踪和分析系统，要求实现以下功能：
### 分析
1. 记录接口调用次数（要求可以自定义时间段进行统计，自定义用户进行查询）
2. 记录接口调用结构
3. 记录接口的平均耗时和最高耗时（要求可以自定义时间段进行统计，自定义用户进行查询） // 这个暂时不用实现

### 限制（这个暂时不用实现）
针对用户权限进行调用次数的限制
为了避免请求泛滥，给API设置速度限制很重要。为此 RFC 6585 引入了HTTP状态码429（too many requests）。加入速度设置之后，应该提示用户,流行的方法是使用HTTP的返回头。

下面是几个必须的返回头（依照twitter的命名规则）：

- X-Rate-Limit-Limit :当前时间段允许的并发请求数
- X-Rate-Limit-Remaining :当前时间段保留的请求数
- X-Rate-Limit-Used : 当前时间段已用的请求数
- X-Rate-Limit-Reset :当前时间段剩余秒数

## 7. 过滤&分页（这个根据业务情况设置，比如成绩获取样例）
如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。
下面是一些常见的参数。
```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
```
参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，`GET /zoo/ID/animals` 与 `GET /animals?zoo_id=ID` 的含义是相同的。

## 8. 状态码 & 数据缓存
### 状态码
在Response header中设置http状态码
同时在Response body中：

- 非200时：返回http status码、错误信息、header（可选）
- 200时：返回http status码、错误code、错误信息、header（可选）、debug（可选）

服务器向用户返回的状态码和提示信息，本设计规范会用到的有以下这些（方括号中是该状态码对应的HTTP动词）。

> * 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
> 　
> * 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
> 　
> * 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
> 　
> * 204 NO CONTENT - [DELETE]：用户删除数据成功，没有需要返回的内容，不需要刷新当前视图。**但是要真这么写的话，dingo中一旦用了204就什么都不返回了，包括code和msg，随机应变，所以我们api中还是用200**
>　
> * 206 Partial Content - [GET]：请求成功，但根据请求头只返回了部分内容。比如说，我们下载一部片，共有 10 部分，我们把请求也分成了 10 次（防止一次请求过大），这时候服务器就可以返回 206 并在其头部告诉我们这是哪一部分，然后再根据这个信息进行拼装。
> 　
> * 304 Not Modified - [GET]：请求的数据没有更新。
> 　
> * 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的，所有不想明确区分的客户端请求出错都可以返回 400，比如参数错误
> 　
> * 401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
> 　
> * 403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的，比如低权限账户请求了高权限账户才能请求的接口。
> 　
> * 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
> 　
> * 405 Method Not Allowed - [*]：用户发出的请求方式是不被允许通过的。
> 　
> * 408 Request Timeout - [*]：客户端请求超时。我们想 POST 创建一个用户，虽然建立了连接，但是网络不好，服务器在规定时间内没有得到我们的请求信息，这时候服务器可以返回 408 告诉我们超时了。然后我们可以重新发送请求。
> 　
> * 409 Conflict - [*]：请求冲突。比如说，服务器要求不同用户不能重名，服务器已经有了一个名叫小伟的用户，这时候我们又想创建一个名叫小伟的用户，服务器可以返回 409，告诉我们冲突了，也可以在 body 中明确告知是什么冲突了。
> 　
> * 413 Request Entity Too Large -[POST/PUT/PATCH]：请求体过大。比如说，服务器要求上传文件不能超过 5M，但是我们 POST 了 10M，这时候就返回 413。
> 　
> * 429 too many requests -[*]：请求速度过快、请求次数已达帐号上限。
> 　
> * 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
> 　
> * 503 Service Unavailable - [*]：服务暂时不可用。比如说，服务器正好在更新代码重启。

状态码的完全列表参见[这里](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。

> * 1xx范围的状态码是保留给底层HTTP功能使用的，并且估计在你的职业生涯里面也用不着手动发送这样一个状态码出来。
> 　
> * 2xx范围的状态码是保留给成功消息使用的，你尽可能的确保服务器总发送这些状态码给用户。
> 　
> * 3xx范围的状态码是保留给重定向用的。大多数的API不会太常使用这类状态码，但是在新的超媒体样式的API中会使用更多一些。
> 　
> * 4xx范围的状态码是保留给客户端错误用的。例如，客户端提供了一些错误的数据或请求了不存在的内容。这些请求应该是幂等的，不会改变任何服务器的状态。
> 　
> * 5xx范围的状态码是保留给服务器端错误用的。这些错误常常是从底层的函数抛出来的，并且开发人员也通常没法处理。发送这类状态码的目的是确保客户端能得到一些响应。收到5xx响应后，客户端没办法知道服务器端的状态，所以这类状态码是要尽可能的避免。

### 关于202（这个如果没有需要插入数据的地方，就暂时不用管）
对耗时的异步任务，服务器端接受客户端传递的参数后，应返回创建成功的任务资源，其中包含了任务的执行状态。客户端可以轮训该任务获得最新的执行进度。
```
提交任务：
POST /batch-publish-msg
[{"from":0,"to":1,"text":"abc"},{},{}...]

返回：
{"taskId":3,"createBy":"Anonymous","status":"running"}

GET /task/3
{"taskId":3,"createBy":"Anonymous","status":"success"}
```
如果任务的执行状态包括较多信息，可以把“执行状态”抽象成组合资源，客户端查询该状态资源了解任务的执行情况。
```
提交任务：
POST /batch-publish-msg
[{"from":0,"to":1,"text":"abc"},{},{}...]

返回：
{"taskId":3,"createBy":"Anonymous"}

GET /task/3/status
{"progress":"50%","total":18,"success":8,"fail":1}
```

### 关于 304 （这个暂时不用管）
大部分接口应该在响应头中携带 `Last-Modified`, `ETag`, `Vary`, `Date` 信息，客户端可以在随后请求这些资源的时候，在请求头中使用 `If-Modified-Since`, `If-None-Match` 等请求头来确认资源是否经过修改。

如果资源没有进行过修改，那么就可以响应 `304 Not Modified` 并且不在响应实体中返回任何内容。例如：
```
curl -i https://api.github.com/user
HTTP/1.1 200 OK
Cache-Control: private, max-age=60
ETag: "644b5b0155e6404a9cc4bd9d8b1ae730"
Last-Modified: Thu, 05 Jul 2012 15:31:30 GMT
Status: 200 OK
Vary: Accept, Authorization, Cookie
X-RateLimit-Limit: 5000
X-RateLimit-Remaining: 4996
X-RateLimit-Reset: 1372700873

curl -i https://api.github.com/user -H 'If-None-Match: "644b5b0155e6404a9cc4bd9d8b1ae730"'
HTTP/1.1 304 Not Modified
Cache-Control: private, max-age=60
ETag: "644b5b0155e6404a9cc4bd9d8b1ae730"
Last-Modified: Thu, 05 Jul 2012 15:31:30 GMT
Status: 304 Not Modified
Vary: Accept, Authorization, Cookie
X-RateLimit-Limit: 5000
X-RateLimit-Remaining: 4996
X-RateLimit-Reset: 1372700873

curl -i https://api.github.com/user -H "If-Modified-Since: Thu, 05 Jul 2012 15:31:30 GMT"
HTTP/1.1 304 Not Modified
Cache-Control: private, max-age=60
Last-Modified: Thu, 05 Jul 2012 15:31:30 GMT
Status: 304 Not Modified
Vary: Accept, Authorization, Cookie
X-RateLimit-Limit: 5000
X-RateLimit-Remaining: 4996
X-RateLimit-Reset: 1372700873
```
---
### 数据缓存（这个暂时不用管）
#### Cache-Control
**“no-cache”和“no-store”**

`no-cache`表示必须先与服务器确认返回的响应是否发生了变化，然后才能使用该响应来满足后续对同一网址的请求。因此，如果存在合适的验证令牌 (ETag)，`no-cache` 会发起往返通信来验证缓存的响应，但如果资源未发生变化，则可避免下载。

相比之下，`no-store`则要简单得多。它直接禁止浏览器以及所有中间缓存存储任何版本的返回响应，例如，包含个人隐私数据或银行业务数据的响应。每次用户请求该资产时，都会向服务器发送请求，并下载完整的响应。

**“public”与“private”**

如果响应被标记为`public`，则即使它有关联的 HTTP 身份验证，甚至响应状态代码通常无法缓存，也可以缓存响应。大多数情况下，`public`不是必需的，因为明确的缓存信息（例如`max-age`）已表示响应是可以缓存的。

相比之下，浏览器可以缓存`private`响应。不过，这些响应通常只为单个用户缓存，因此不允许任何中间缓存（CDN等）对其进行缓存。例如，用户的浏览器可以缓存包含用户私人信息的 HTML 网页，但 CDN 却不能缓存。

**“max-age”**

指令指定从请求的时间开始，允许获取的响应被重用的最长时间（单位：秒）。例如，`max-age=60`表示可在接下来的 60 秒缓存和重用响应。

#### **选择合适的Cache-Control**
![决策树](http://osv9x79o9.bkt.clouddn.com/17-11-7/30809467.jpg)

| Cache-Control指令 | 说明 |
| --- | --- |
| max-age=86400 | 浏览器以及任何中间缓存均可将响应（如果是“public”响应）缓存长达 1 天（60 秒 x 60 分钟 x 24 小时）。 |
| private, max-age=600 | 客户端的浏览器只能将响应缓存最长 10 分钟（60 秒 x 10 分钟）。
| no-store | 不允许缓存响应，每次请求都必须完整获取。 |

#### **废弃和更新缓存响应**
*如何才能鱼和熊掌兼得：客户端缓存和快速更新？*您可以在资源内容发生变化时更改它的网址，强制用户下载新响应。通常情况下，可以通过在文件名中嵌入文件的指纹或版本号来实现 - 例如 `style.x234dff.css`。

![一个缓存样例](http://osv9x79o9.bkt.clouddn.com/17-11-7/38495502.jpg)

- HTML 被标记为`no-cache`，这意味着浏览器在每次请求时都始终会重新验证文档，并在内容变化时获取最新版本。此外，在 HTML 标记内，您在 CSS 和 JavaScript 资产的网址中嵌入指纹：如果这些文件的内容发生变化，网页的 HTML 也会随之改变，并会下载 HTML 响应的新副本。

- 允许浏览器和中间缓存（例如 CDN）缓存 CSS，并将 CSS 设置为 1 年后到期。请注意，您可以放心地使用 1 年的“远期过期”，因为您在文件名中嵌入了文件的指纹：CSS 更新时网址也会随之变化。

- JavaScript 同样设置为 1 年后到期，但标记为 private，这或许是因为它包含的某些用户私人数据是 CDN 不应缓存的。

- 图像缓存时不包含版本或唯一指纹，并设置为 1 天后到期。

---

相关资料：

- [RFC 7232](http://tools.ietf.org/html/rfc7232)
- [HTTP 缓存 - Google Developers](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)
- [RFC 2616 中缓存过期时间的算法](http://tools.ietf.org/html/rfc2616#section-13.2.3), [MDN 版](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching_FAQ), [中文版](http://blog.csdn.net/woxueliuyun/article/details/41077671)
- [HTTP 协议中 Vary 的一些研究](https://www.imququ.com/post/vary-header-in-http.html)
- [Cache Control 與 ETag](https://blog.othree.net/log/2012/12/22/cache-control-and-etag/)


## 9. 预期返回（操作*实体*时返回，操作*联系*是可以不用返回）
## 如api权限系统里面，item和Collection是*实体*，他们之间的关系是*联系*
当使用不同的HTTP动词向服务器请求时，客户端需要在返回结果里面拿到一系列的信息，例如：

> * GET /collection：返回资源对象的列表（数组）
> 　
> * GET /collection/resource：返回单个资源对象
> 　
> * POST /collection：返回新生成的资源对象
> 　
> * PUT /collection/resource：返回完整的资源对象
> 　
> * PATCH /collection/resource：返回完整的资源对象
> 　
> * DELETE /collection/resource：返回一个空文档

总是在响应码为200或是201时返回所有可用资源, 包含 `PUT/PATCH` 和 `DELETE` 请求, 例如:
```
$ curl -X DELETE \  
  https://service.com/apps/1f9b/domains/0fd4

HTTP/1.1 200 OK
Content-Type: application/json;charset=utf-8
...
{
  "created_at": "2012-01-01T12:00:00Z",
  "hostname": "subdomain.example.com",
  "id": "01234567-89ab-cdef-0123-456789abcdef",
  "updated_at": "2012-01-01T12:00:00Z"
}
```
当请求状态码为202（已加入异步队列）时，不返回所有可用资源,例如：
```
$ curl -X DELETE \  
  https://service.com/apps/1f9b/dynos/05bd

HTTP/1.1 202 Accepted
Content-Type: application/json;charset=utf-8
...
{}
```

---

## 以下第 10 点可以看兴趣实现，然后除了第 16 和第 18 点需要注意，其他的都暂时不用管。

## 10. Hypermedia & 超文本驱动
超文本驱动可以看做是Hypermedia的更高级实现。
### Hypermedia
即返回结果中提供链接，连向其他API方法，这样做的好处是：用户不查文档，也知道下一步应该做什么。比如，当用户向api.example.com的根目录发出请求，会得到一个关于API文档的说明，就像[api.github.com](https://api.github.com)

### 超文本驱动
客户端不再需要将某些接口的 URI 硬编码在代码中，唯一需要存储的只是 API 的 HOST 地址，能够非常有效的降低客户端与服务端之间的耦合，服务端对 URI 的任何改动都不会影响到客户端的稳定。
目前有几种方案试图实现这个效果：

- [JSON HAL](http://tools.ietf.org/html/draft-kelly-json-hal-07) ，示例可以参考 [JSON HAL 作者自己的介绍](http://stateless.co/hal_specification.html)
- [GitHub API 使用的方案](https://developer.github.com/v3/#hypermedia) ，应该是一种 JSON HAL 的变体
- [JSON API](http://jsonapi.org/) ，（这里有 [@迷渡](https://github.com/justjavac) 发起的 [中文版](http://jsonapi.org.cn/) ），另外一种类似 JSON HAL 的方案
- [Micro API](http://micro-api.org/) ，一种试图与 [JSON-LD](http://json-ld.org/) 兼容的方案

目前所知的方案都实现了发现资源的功能，服务端同时需要实现 OPTIONS 方法，并在响应中携带 Allow 头来告知客户端当前拥有的操作权限。

## 11. 数据返回格式
采用json。
在header中添加`Content-Type=application/json`。

## 12. 清晰的文档
api必须拥有清晰的文档。

## 13. 鉴权
JWT

## 14. 易于测试
API需要方便开发者在页面中进行测试。

## 15. Enveloping
有两种情况是应该使用envelope的。

- 如果API使用者确实无法访问返回头
- API需要支持交叉域请求（通过jsonp）,**本工作室Api需要跨域时请用CORS**

jsonp请求在请求的url中包含了一个`callback`函数参数。如果给出了这个参数，那么API应该返回200，并且把真正的状态码放到返回值里面（包装在信封里），例如：
```
callback_function({
  status_code: 200,
  next_page: "https://..",
  response: {
    ... actual JSON response body ... 
  }
})
```

同样为了支持无法方法返回头的API使用者，可以允许`envelope=true`这样的参数,例如:
```
GET /api/v1/users/does-not-exist?envelope=true
```
返回码为200，真正的返回码以及其他header信息放到返回的信息中：
```
{
  "status": 404,
  "headers": {
    "Rate-Limit-Limit": 100,
    "Rate-Limit-Remaining": 50,
    "Rate-Limit-Used": 0,
    "Rate-Limit-Reset": 25
  },
  "response": {
    "message": "Not Found"
  }
}
```

## 16. 数据校验
以json为例，当客户端向服务器发出`post`,`put`或`patch`请求时，通常会同时给服务器发送json格式的相关数据，服务器在做数据处理之前，先做数据校验，是最合理和安全的前后端交互。如果客户端发送的数据不正确或不合理，服务器端经过校验后直接向客户端返回400错误及相应的数据错误信息即可。常见的数据校验包括：

- 数据类型校验，如字段类型如果是int，那么给字段赋字符串的值则报错
- 数据格式校验，如邮箱或密码，其赋值必须满足相应的正则表达式，才是正确的输入数据
- 数据逻辑校验，如数据包含出生日期和年龄两个字段，如果这两个字段的数据不一致，则数据校验失败

以上三种类型的校验，数据逻辑校验最为复杂，通常涉及到多个字段的配合，或者要结合用户和权限做相应的校验。

## 17. CORS——解决跨域
在RESTful API开发中，主要是为js服务的，CORS即解决javascript 调用 RESTful API时的跨域问题。具体可以参考[阮一峰老师的跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)

## 18. 命名规范
1. 不用大写
2. RESTful API 应具备良好的可读性，当url中某一个片段（segment）由多个单词组成时，建议使用 - 来隔断单词，而不是使用 _，即：

```
# Good
/api/featured-post/

# Bad
/api/featured_post/
```


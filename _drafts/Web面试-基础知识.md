---
title: Web面试-基础知识
tags:
  - Web
  - 基础
categories:
  - Web
  - 基础
date: 2018-03-30 23:15:40
thumbnail:
---

web基础知识整理。

# 后端

## 一、PHP

### 1. PECL、PEAR 和 Composer

**PEAR：**PHP扩展和应用仓库，大部分是用 PHP 写的，是 PHP 的上层扩展。

**PECL：**PHP 扩展库，其中扩展是用 C 写的，是 PHP 的底层扩展。

**Composer：**类似PEAR，包管理工具，可以自动加载和管理包的依赖。



### 2. 变量

#### 赋值

```php
$foo = 'jochen'; # 传值赋值
$bar = &$foo;    # 引用赋值，相当于取别名

$bar = 'coco';   # $foo、$bar 的值都为 coco
```

#### PHP超级全局变量（9个）

```php
$GLOBALS	# 储存全局作用域中的变量
$_SERVER　　 # 获取服务器相关信息
$_REQUEST　　# 获取POST和GET请求的参数
$_POST　　   # 获取表单的POST请求参数
$_GET　　    # 获取表单的GET请求参数
$_FILES　　  # 获取上传文件的的变量
$_ENV　　    # 获取服务器端环境变量的数组

$_COOKIE　　 # 浏览器cookie的操作
$_COOKIE["user"]; # 获取cookie
setcookie(name, value, expire, path, domain); # 设置cookie
setcookie("user", "", time()-3600);  # 删除cookie：

$_SESSION　　# 服务端session的操作
# 使用session前一定要session_start()启动session
# 储存session：$_SESSION["name"]="King";//数组操作
# 销毁session：unset($_SESSION["name"]);//销毁一个
session_destroy()和unset($_SESSION);//销毁所有的session

```

#### PHP魔术变量（8个）

```php
__LINE__　　	# 文件中的当前行号。

__FILE__　　	# 文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。

__DIR__　　 	# 文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录。

__FUNCTION__　# 常量返回该函数被定义时的名字

__CLASS__　　	# 常量返回该类被定义时的名字（区分大小写）。

__METHOD__　　# 类的方法名（PHP 5.0.0 新加）。返回该方法被定义时的名字（区分大小写）。

__NAMESPACE__　# 当前命名空间的名称（区分大小写）。此常量是在编译时定义的（PHP 5.3.0 新增）。
```

#### PHP魔术函数（13个）

```php
__construct()　　	# 实例化对象时被调用，当__construct和以类名为函数名的函数同时存在时，__construct将被调用，另一个不被调用。

__destruct()　　  # 当删除一个对象或对象操作终止时被调用。

__call()　　　　  # 对象调用某个方法，若方法存在，则直接调用；若不存在，则会去调用__call函数。

__get()　　　　   # 读取一个对象的属性时，若属性存在，则直接返回属性值；若不存在，则会调用__get函数。

__set()　　　　   # 设置一个对象的属性时，若属性存在，则直接赋值；若不存在，则会调用__set函数。

__toString()　　  # 打印一个对象的时被调用。如echo $或obj;或print $obj;

__clone()　　　   # 克隆对象时被调用。如：$t=new Test();$t1=clone $t;

__sleep()　　　   # serialize之前被调用。若对象比较大，想删减一点东东再序列化，可考虑一下此函数。

__wakeup()　　    # unserialize时被调用，做些对象的初始化工作。

__isset()　　　   # 检测一个对象的属性是否存在时被调用。如：isset($c->name)。

__unset()　　　　 # unset一个对象的属性时被调用。如：unset($c->name)。

__set_state()　　 # 调用var_export时，被调用。用__set_state的返回值做为var_export的返回值。

__autoload()　　  # 实例化一个对象时，如果对应的类不存在，则该方法被调用。
```



### 3. 数据类型

- 字符串
- 整数
- 浮点数
- 逻辑
- 数组
- 对象
- NULL



### 4. 新特性





###5. 常用函数

#### 数组函数

- **array_combine：**数组合并
- **array_count_values：**统计数组中所有值出现的次数
- **array_key_exists：**检查某个数组中是否存在指定的键名
- **in_array：**搜索数组中是否存在指定的值
- **array_pop：**删除最后一个元素，出栈
- **array_push：**在尾部插入一个元素，入栈
- **array_reverse：**返回翻转顺序的数组
- **array_search：**在数组中搜索某个键值，并返回对应的键名
- **array_shift：**删除数组开头第一个元素
- **array_unshift：**在数组开头插入一个或多个元素
- **array_unique：**去除数组中的重复元素
- **array_flip：**反转键值
- **arsort：**键值数组按键值降序排序
- **asort：**键值数组按键值升序排序
- **compact：**以变量名为 key，值为 value 创建数组
- **krsort：**按键名降序排序
- **ksort：**按键名升序排序
- **rsort：**数值数组进行降序排序
- **sort：**数值数组进行升序排序
- **unset($arry)：**释放一个数组

#### 字符串函数

- **strrev：**字符串反转
- **number_format：**千分位格式化，如 50,000,000
- **iconv：**编码转换
- **explode：**根据指定字符打散为数组
- **join / implode：**explode的反函数
- **str_replace：**字符串替换
- **strstr：**查找字符串在另一个字符串中第一次出现的位置
- **substr：**字符串截取
- **substr_count：**计算串中出现的子串个数
- **filter_var：**PHP自带的过滤函数

#### 安全函数

- **htmlentities**  和  **html_entity_decode**

  前者把所有HTML实体转化为实体字符，后者反过来。

- **htmlspecialchars**和 **htmlspecialchars_decode**

  前者把预定义的HTML实体转化为实体字符，后者反过来，只转化固定的几个字符，没有上面那个多。

- **addslashes** 和 **stripslashes**

  前者在预定义字符前加上反斜杠 \\，后者相反。

- **strip_tags：**直接去除其中的HTML标签。

- **trim：**去除空格

- **mysqli_real_escape_string：**mysql转义特殊的输入字符

#### 序列化

- **serialize** 和 **unserialize**：这种序列化有利于存储或传递 PHP 的值，同时不丢失其类型和结构。
- **json_encode** 和 **json_decode**：
  - json_encode( '中文', JSON_UNESCAPED_UNICODE );  // 中文编码

#### 其他函数

- **date("Y-m-d H:i:s",time()) ：**格式化时间戳
- **isset()、empty()：**判断一个变量是否有设置
- **define(“MYCONSTANT”, 100)：**常量定义
- **strtotime(“+1 week")：** 函数将任何英文文本的日期或时间描述解析为 Unix 时间戳



### 6. 一些操作

#### 截取扩展

```php
$url ="http://www.baidu.com/abc/de/fg.php/?id=1";
//获得文件目录名字,http://www.baidu.com/abc/de/fg.php
echo $path = pathinfo($url,PATHINFO_DIRNAME );
//获得文件的扩展名,php
$ex = pathinfo($path,PATHINFO_EXTENSION);
echo $ex;
```

#### 进一取整，退一取整

```php
// 进一
ceil()
// 退一
floor()
// 四舍五入并设置精度
round(1.95583, 2) // 1.96
```

#### IP获取

```php
echo $_SERVER[‘REMOTE_ADDR’];	# 客户端IP，这个如果你和主机在同一个局域网，获取的是局域网地址，如果服务器在公网，而你在内网，获得的是你的出口IP
echo $_SERVER[‘SERVER_ADDR’];	# 服务器IP

$ip = filter_var($originIp, FILTER_VALIDATE_IP，FILTER_FLAG_IPV4)  # 宽松获取IP时的过滤，第三个参数判断IP是否合法  

ip2long() # 存储到数据库时可以这样转换一下，搜索和排序更快，如果出现负数是因为系统32或64位版本的问题，可以自己实现一个
long2ip() # 转换回来  
```

==ip2long 的自己实现==

**一、没有使用代理服务器的PHP获取客户端IP情况：** 

REMOTE_ADDR = 客户端IP
HTTP_X_FORWARDED_FOR = 没数值或不显示

**二、使用透明代理服务器的情况：Transparent Proxies**

REMOTE_ADDR = 最后一个代理服务器 IP
HTTP_X_FORWARDED_FOR = 客户端真实 IP （经过多个代理服务器时，这个值类似：221.5.252.160, 203.98.182.163, 203.129.72.215）
这类代理服务器还是将客户端真实的IP发送给了访问对象,无法达到隐藏真实身份的目的.

**三、使用普通匿名代理服务器的PHP获取客户端IP情况：Anonymous Proxies**

REMOTE_ADDR = 最后一个代理服务器 IP
HTTP_X_FORWARDED_FOR = 代理服务器 IP （经过多个代理服务器时，这个值类似：203.98.182.163, 203.98.182.163, 203.129.72.215）
这种情况下隐藏了客户端的真实IP,但是向访问对象透露了客户端是使用代理服务器访问它们的.

**四、使用欺骗性代理服务器的情况：Distorting Proxies**

REMOTE_ADDR = 代理服务器 IP
HTTP_X_FORWARDED_FOR = 随机的 IP（经过多个代理服务器时,这个值类似：220.4.251.159, 203.98.182.163, 203.129.72.215）
这种情况下同样透露了客户端是使用了代理服务器,但编造了一个虚假的随机IP（220.4.251.159）代替客户端的真实IP来欺骗它.

**五、使用高匿名代理服务器的PHP获取客户端IP情况：High Anonymity Proxies (Elite proxies)**

REMOTE_ADDR = 代理服务器 IP
HTTP_X_FORWARDED_FOR = 没数值或不显示

无论是REMOTE_ADDR还是HTTP_FORWARDED_FOR，这些头消息未必能够取得到,因为不同的浏览器不同的网络设备可能发送不同的IP头消息.因此PHP使用`$_SERVER["REMOTE_ADDR"]` 、`$_SERVER["HTTP_X_FORWARDED_FOR"]` 获取的值可能是空值也可能是“unknown”值.

#### 文件读取

```php
$file = fopen("welcome.txt", "r") or exit("无法打开文件!");
// 读取文件每一行，直到文件结尾
while(!feof($file))
{	// 逐行
    echo fgets($file). "<br>";
    // 逐字符
    echo fgetc($file);
}
fclose($file);

chmod($file,$mode); // 改变文件权限
```

#### 文件包含

**include：**缺失只会生成警告（E_COMPILE_ERROR）

**require：**生成致命错误（E_WARNING）

#### 错误显示

**错误级别：**E_ERROR、E_WARNING、E_NOTICE

```php
# php.ini
display_errors = On
    
# php-fpm.conf 或 www.conf
php_flag[display_errors] = on
    
# 代码中临时修改配置
ini_set("error_reporting", "E_ALL & ~E_NOTICE") # 所有错误处理notice
ini_set("display_errors", "On");    # ini_set("max_execution_time",  "180"); 修改脚本可执行时间
```



### 7. 面向对象

#### 接口和抽象的区别

**接口**

- 像一个标准，所有定义的方法必须是公有的，子类必须实现接口定义的所有方法。

**抽象**

- 子类必须实现父抽象类的所有方法，而且权限声明一样或者更为宽松，否则子类也是一个抽象类。

> 抽象类和接口类的区别：
>
> 抽象类-->像xxx一样，强调所属
> 接    口-->能xxx这样，强调功能的实现
>
> 接口：
>
> - 只能定义函数声明
> - 不能声明实例变量
> - 一个类可以实现多个接口
> - 接口之间可以继承
>
> 抽象：
>
> - 可以定义普通方法（缺省方法）
>
> 相同点：
>
> - 都不能实例化

#### 常量

```
const constant = '常量值';
```

#### Final

父类中的方法被声明为 final，则子类无法覆盖该方法。如果一个类被声明为 final，则不能被继承。

#### 父类的构造方法

PHP 不会在子类的构造方法中自动的调用父类的构造方法。要执行父类的构造方法，需要在子类的构造方法中调用 **parent::__construct()** 。



### 8. 四层结构



### 9. 多线程





## 二、 数据库

### 1. MYSQL 数据库

#### 基本概念

- **索引：**使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
- **参照完整性:** 参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。
- **ACID：**
  - 原子性：事务
  - 一致性：数据库的中的事物只会从一种合法状态变为另一种合法状态
  - 隔离性：并发执行和串行执行对事物的影响是一致的
  - 持久性：数据一旦被提交成功就永久保存，不会受断电和崩溃的影响

#### 数据库优化原则

1. 选择合适的数据类型：最小、最简单、最合适，甚至可以把数据转换为合适格式再选择合适的数据类型
2. 尽量避免使用NULL，NULL会让索引统计变得复杂
3. 合适的索引设计
4. 不要太追求范式理论，允许适当的冗余，减少join的使用，用空间换时间
5. 访问频率较低的大字段拆分出数据表
6. 大表考虑水平切分（分表）

####大流量优化

1. 优化 sql 和索引，可以的话检查表结构是否还有优化的空间（不过这个改起来牵扯的东西比较多）
2. 程序内部优化，尽量减少数据库请求次数
3. 上缓存，memcached、redis
4. 主主热备、主从热备、一主多从等方式，读写分离，减轻读写压力
5. mysql 自带的分区表（分治的思想）
6. 垂直拆分：根据你模块的耦合度，将一个系统分为多个系统，也就是分布式系统
7. 水平切分：分表

---

7. 禁止外部的盗链
8. 控制大文件的下载

#### 其他优化注意

1. 对性别、身份存储时可选择枚举ENUM类型
2. 对ip地址可以将其转换为整型存储
3. Mysql 5.7.7 推出了新的 Json 数据类型

#### 优化——分表分库

#### MyISAM 和 InnoDB

#### MYSQL 常用函数

#### 字段类型

- varchar 不定长，char 定长并且用空格补位。varchar查询效率高于char，

- 时间戳的问题：

  - int 值存储：速度很快。**4字节**

  - datetime 存储：和timestamp相反，和字符串比较较快，使用UNIX_TIMESTAMP较慢，**8字节**

  - timestamp 存储：**4字节**，能够存储时区信息，可以自动更新，建议用这个

    ```mysql
    # timestamp 直接和字符串日期比效率比较低
    d_timestamp>'1970-01-05 23:06:40' AND d_timestamp<'1970-01-08 06:40:00'

    # 使用内置函数 UNIX_TIMESTAMP()，查询效率比较高
    UNIX_TIMESTAMP(d_timestamp)>400000 AND UNIX_TIMESTAMP(d_timestamp)<600000
    ```

    ​

#### 细节

- 判断字段是否为空要是用：**IS NULL** 和 **IS NOT NULL**
- **LIKE** 语句支持正则表达式
- **SHOWCREATE TABLE 表名 \G** 查看表的完整结构
- **count(1)** 和 **count(*)**，效果上没有区别，但是前者性能更好，都是用来获取符合查询条件的结果的**数目**的。

#### 事务

1. 用 BEGIN, ROLLBACK, COMMIT来实现

- **BEGIN** 开始一个事务
- **ROLLBACK** 事务回滚
- **COMMIT** 事务确认

2. 直接用 SET 来改变 MySQL 的自动提交模式:

- **SET AUTOCOMMIT=0** 禁止自动提交
- **SET AUTOCOMMIT=1** 开启自动提交

#### 索引

**优点：**大大提高MYSQL的检索速度

**缺点：**降低了表的更新速度

**单列索引：**一个索引只包含单个列

**组合索引：**一个索引包含多个列

**索引的创建：**

```mysql
# 普通索引的创建
CREATE INDEX indexName ON mytable(username(length)); 	# 直接创建
ALTER table tableName ADD INDEX indexName(columnName)	# 修改表结构创建
CREATE TABLE mytable(  								  # 创建表的时候指定
	ID INT NOT NULL,   
	username VARCHAR(16) NOT NULL,  
	INDEX [indexName] (username(length))  
);  

# 唯一索引
CREATE UNIQUE INDEX indexName ON mytable(username(length)) 
ALTER table mytable ADD UNIQUE [indexName] (username(length))
CREATE TABLE mytable(  
	ID INT NOT NULL,   
	username VARCHAR(16) NOT NULL,  
	UNIQUE [indexName] (username(length))  
);  
```

**索引的优化原则：**

#### 表复制

```mysql
# 复制表结构、主键、索引等，但是不含数据
CREATE TABLE newadmin LIKE admin 

# 以下这个会复制数据，但其实只是把select的结果创建一个表，所以不会有主键和索引
CREATE TABLE newadmin AS ( SELECT * FROM admin ) 

# 完整的复制表和数据
CREATE TABLE newadmin LIKE admin; INSERT INTO newadmin SELECT * FROM admin; 

# 跨数据库的操作
CREATE TABLE newadmin LIKE shop.admin; CREATE TABLE newshop.newadmin LIKE shop.admin; 

# 仅拷贝一些字段，并且改名
CREATE TABLE newadmin AS ( SELECT id, username AS uname, password AS pass FROM admin ) 
```

#### 数据库备份

mysql 双机异地热备份：主从、主主

双机热备：保持两个数据库的状态自动同步。对任何一个数据库的操作都自动应用到另外一个数据库,始终保持两个数据库数据一致。 好处有：灾备和负载均衡。需要有相同的初态才有意义。目前也不是完美的。

复制方式：基于语句的复制，基于行的复制，混合型复制

主从热备：从（Slave）读取主（Master）的日志，将主机上的修改都做一次。

主主热备：从主设置一次，主从设置一次。双向设置，就可以做到主主热备。

```mysql
show master status/G  # 查看主机状态
show slave  status/G  # 查看从机状态

# 创建从机用于读取日志数据的账号，IP设置为从机IP，更安全
grant replication slave on *.* to 'repl_user'@'192.***.***.***' identified by 'hj34$%&;mnkb'; 

# Master 打开数据库配置文件 my.cnf
# binlog-do-db 表示关注的数据库
# binlog-ignore-db 表示忽略的数据库
# auto-increment-increment 为了防止自增主键的冲突，如果有10台机器时，设置这里为10
# auto-increment-offset=1  为了防止自增主键的冲突，综合上面，第一台机器第一个为1，第二个就是11了

# Slave 打开数据配置文件 my.cnf
# server-id 必须保证每个服务器不一样
# replicate-do-db 可以指定需要复制的数据库
# replicate-ignore-db 复制时需要排除的数据库
# relay_log 中继日志的名字，从机需要将主机上的日志先复制到这
# log-slave-updates 从机是否记录自己的 binarylog，如果是主主热备，需要开启这个选项

# 初态同步
# Master先锁定数据库
FLUSH TABLES WITH READ LOCK;
# Master导出数据表结构和数据

# 在Salve导入数据，存在远程导出的命令，用管道完成的
# 设置主机
CHANGE MASTER TO 
MASTER_HOST='192.***.***.***', 
MASTER_USER='repl_user', 
MASTER_PASSWORD='hj3****', 
MASTER_LOG_FILE='mysql-bin.000004', 
MASTER_LOG_POS=7145;	# 在 show 命令中可以看到
# 重启mysql，查看slave线程是否开启
Slave_IO_Running: Yes
Slave_SQL_Running: Yes

```

- 主从形式：
  - 一主一从
  - 一主多从：扩展系统读取性能
  - 多主一从：5.7开始支持
  - 主主复制
  - 联级复制
- 问题及解决：
  - 主库宕机后，数据丢失
    - 半同步复制：通过校验，确保从库响应成功才响应客户端，会降低性能
  - 主库写压力大，因从库只有一个 sql 线程来持久化，复制可能延迟
    - 并行复制：从库多线程来持久化

```shell
# 导入和导出
mysqldump -u root -p 密码 表名 > dump.txt
mysql -u root -p database_name < dump.txt
mysqlimport -u root -p --local database_name dump.txt
```





### 2. 其他概念

#### PDO

面向对象的形式为PHP访问数据库定义了一个轻量级的一致接口，可以让你不用去关系具体要连接的数据库类型，平滑的切换使用的数据库。

#### Mysqli

Mysql驱动的升级版，面向对象的形式，但支持 Mysql。

#### ORM

对象关系映射，把两种不同类型系统的数据关系进行映射。

在数据库方面的具体应用，就是把数据库中的表映射到应用中的模型。



## 三、面向对象









## 四、设计模式

设计模式（Design pattern）代表了最佳的实践，。设计模式是软件开发人员在软件开发过程中面临的一般问题的解决方案。这些解决方案是众多软件开发人员经过相当长的一段时间的试验和错误总结出来的。为了重用代码、让代码更容易理解、保证代码可靠性和可维护性。

设计模式可以分为三大类：

- **创建型**模式：这些设计模式提供了一种在创建对象的同时隐藏创建逻辑的方式，而不是使用 new 运算符直接实例化对象。这使得程序在判断针对某个给定实例需要创建哪些对象时更加灵活。
- **结构型**模式：这些设计模式关注类和对象的组合。继承的概念被用来组合接口和定义组合对象获得新功能的方式。
- **行为型**模式：这些设计模式特别关注对象之间的通信。

五大基本原则：

- 单一职责原则(SRP)
- 开闭原则(OCP)
- 里氏替换原则(LSP)
- 依赖倒置原则(DIP)
- 接口隔离原则(ISP)

### 1. 工厂模式

提供了一种创建对象的最佳方式。在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

用代码说话就是：

```php
// 这个不合理，一旦 Power 的参数个数改变，那么不仅 Sample 类要改，连用到它的类也要改
class Superman
{
    protected $power;

    public function __construct()
    {
        $this->power = new Power(999, 100);
    }
}
```

所以改成变成：

```php
// 文件1-工厂类
class SuperModuleFactory
{
    public function makeModule($moduleName, $options)
    {
        switch ($moduleName) {
            case 'Fight':     return new Fight($options[0], $options[1]);
            case 'Force':     return new Force($options[0]);
            case 'Shot':     return new Shot($options[0], $options[1], $options[2]);
        }
    }
}

// 文件2-构造类
class Superman
{
    protected $power;

    public function __construct(array $modules)
    {
        // 初始化工厂
        $factory = new SuperModuleFactory;

        // 通过工厂提供的方法制造需要的模块
        foreach ($modules as $moduleName => $moduleOptions) {
            $this->power[] = $factory->makeModule($moduleName, $moduleOptions);
        }
    }
}

// 文件3-使用类
$superman = new Superman([
    'Fight' => [9, 100], 
    'Shot' => [99, 50, 2]
    ]);
```

### 2. 抽象工厂模式

围绕一个超级工厂创建其他工厂，该超级工厂又称为其他工厂的工厂。

定义一个抽象工厂类，其他工厂继承自此工厂。

### 3. 单例模式

保证一个类仅有一个实例，并提供一个访问它的全局访问点。无法通过 new 来创建这个类的实例，因为其构造函数是 **private** 的。一般通过 **getInstance()** 的方法来获取它们的实例。

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}  
```

使用场景：

- 操作文件的时候，需要使用句柄，可以通过单例模式来返回这个句柄，节省内存开销，避免对文件的多重操作

### 4. 观察者模式

定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

**观察者接口：**

```java
public abstract class Observer {    
    public abstract void update(String msg);
}
```

**第一个观察者：**

```java
public class F_Observer extends Observer {
    public void update(String msg) {
        System.out.println(F_Observer.class.getName() + " : " + msg);
    }
}
```

**第二个观察者：**

```java
public class S_Observer extends Observer {
    public void update(String msg) {
        System.out.println(S_Observer.class.getName() + " : " + msg);
    }
}
```

**第三个观察者：**

```java
public class T_Observer extends Observer {
    public void update(String msg) {
        System.out.println(T_Observer.class.getName() + " : " + msg);
    }
}
```

**被观察者：**

```java
public class Subject {        
    private List<Observer> observers = new ArrayList<>();    //状态改变    
    public void setMsg(String msg) {        
        notifyAll(msg);    
    }   
     //订阅    
    public void addAttach(Observer observer) {        
        observers.add(observer);    
    }    
    //通知所有订阅的观察者    
    private void notifyAll(String msg) {        
        for (Observer observer : observers) {            
            observer.update(msg);        
        }   
    }
}
```

**使用方法：**

```java
public class Main {    
    public static void main(String[] args) {        
        F_Observer fObserver = new F_Observer();        
        S_Observer sObserver = new S_Observer();        
        T_Observer tObserver = new T_Observer();                
        Subject subject = new Subject();        
        subject.addAttach(fObserver);        
        subject.addAttach(sObserver);        
        subject.addAttach(tObserver);                
        subject.setMsg("msg change");    
    }
}
```

### 5. MVC

用于应用程序的分层开发。

- **Model（模型）** - 模型代表一个存取数据的对象或 JAVA POJO。它也可以带有逻辑，在数据变化时更新控制器。
- **View（视图）** - 视图代表模型包含的数据的可视化。
- **Controller（控制器）** - 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。

### 6. 装饰器模式

动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。继承的一个替代模式。

举个栗子，我想吃三明治，首先我需要一根大大的香肠，我喜欢吃奶油，在香肠上面加一点奶油，再放一点蔬菜，最后再用两片面包加一下，很丰盛的一顿午饭，营养又健康，那我们应该怎么来写代码呢？ 
首先，我们需要写一个Food类，让其他所有食物都来继承这个类，看代码：

```java
public class Food {

    private String food_name;

    public Food() {
    }

    public Food(String food_name) {
        this.food_name = food_name;
    }

    public String make() {
        return food_name;
    };
}
```

代码很简单，我就不解释了，然后我们写几个子类继承它：

```java
//面包类
public class Bread extends Food {

    private Food basic_food;

    public Bread(Food basic_food) {
        this.basic_food = basic_food;
    }

    public String make() {
        return basic_food.make()+"+面包";
    }
}

//奶油类
public class Cream extends Food {

    private Food basic_food;

    public Cream(Food basic_food) {
        this.basic_food = basic_food;
    }

    public String make() {
        return basic_food.make()+"+奶油";
    }
}

//蔬菜类
public class Vegetable extends Food {

    private Food basic_food;

    public Vegetable(Food basic_food) {
        this.basic_food = basic_food;
    }

    public String make() {
        return basic_food.make()+"+蔬菜";
    }

}
```

这几个类都是差不多的，构造方法传入一个Food类型的参数，然后在make方法中加入一些自己的逻辑，如果你还是看不懂为什么这么写，不急，你看看我的Test类是怎么写的，一看你就明白了

```java
public class Test {
    public static void main(String[] args) {
        Food food = new Bread(new Vegetable(new Cream(new Food("香肠"))));
        System.out.println(food.make());
    }
}
```

看到没有，一层一层封装，我没从里往外看：最里面我new了一个香肠，在香肠的外面我包裹了一层奶油，在奶油的外面我又加了一层蔬菜，最外面我放的是面包，是不是很形象？ 



## 五、其他概念

### 1. 术语名次

#### REPL

交互式的编程环境。例如Chrome 的控制台，python 的命令行工作环境。

#### JSON-RPC

是一种以json为协议的远程调用服务，具有开发调试简单，多平台通用的特性。

#### AOP

面向切面编程。

这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。

#### GET 、POST、PATCH、PUT、DELETE

- GET 只能传1024字节，且参数会存在url中。一般用户信息获取，安全幂等


- POST 能根据所用脚本的配置文件来改，一般为2M。一般用户创建数据，不是安全幂等的
- PATCH 一般用于更新部分数据，不是安全幂等的
- PUT 一般用户创建或完整更新数据，是安全幂等的
- DELETE 一般用户删除数据，是安全且幂等的



### 2. 测试

#### 单元测试

用来测试单元（一般为函数）是否正确的测试代码。一般是给定一个输入看输出是否符合预期。用来测试小块代码。

#### 文档测试

把测试代码以注释的形式写在函数前面，自动化测试的工具可以自动识别。即其了文档左右，又起了测试作用。

#### 功能测试

用来测试大块代码。

#### HTTP测试

用来测试访问固定链接后的响应和返回数据是否符合预期。

#### 浏览器测试

用来模拟浏览器的一些操作，测试结果是否符合预期。

#### 数据库测试

用来测试需要用到数据库的应用，可以在这过程中插入一些虚拟数据，并设置测试完重置数据库。

分布式

MVC

Session/Cookie





# 前端

## 一、JavaScript

### 1. this

总是指向调用它所在方法的对象。



### 2. 闭包

JavaScript 中子函数可以访问父函数的变量，但是父函数不可以访问子变量。

**能够读取其他函数内部变量的函数**，放到 JavaScript 中就是，定义在函数内部的函数，以为 JavaScript 是链式作用域，即子对象可以访问父对象的数据，但是反过来不行。所以父对象需要访问子对象变量数据时就要用到闭包。

#### 闭包的用途

- 读取函数内部的变量（这个前面解释了）
- 让变量的值始终保存在内存中：如果闭包函数被赋给一个全局变量，那么闭包函数用到的局部变量将不会被回收

#### 闭包的注意点

- 由于闭包会使函数变量保存在内存中是，所以不能滥用闭包
- 由于闭包函数可以在函数外改变函数内的变量，所以要注意你对函数变量的定义是公有还是私有




### 3. call 和 apply

在特定的作用域中调用函数,能改变函数的作用域，实际上是改变函数体内 `this` 的值 。

说通俗一点就是，借你家函数用一下，call 和 apply 效果一行，只是传参不同。

```javascript
foo.call(this, arg1,arg2,arg3) == foo.apply(this, [arg1, arg2, arg3]) == this.foo(arg1, arg2, arg3)
```

**call方法:**
语法：call(thisObj，Object)
定义：调用一个对象的一个方法，以另一个对象替换当前对象。
说明：
call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。

**apply方法:**
语法：apply(thisObj，[argArray])
定义：应用某一对象的一个方法，用另一个对象替换当前对象。
说明：
如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。
如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。



### 4. 自执行函数

创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。这种函数的的定义和调用是一体的。

```JavaScript
// 两种常用写法
(function () { /* code */ } ());
(function () { /* code */ })();
```



### 5. 原生实现一个Ajax，兼容IE



### 6. 数组操作



### 7. 字符串操作

### 8. DOM





## 二、模板引擎

将显示与数据分离，输入模板字符串 + 数据，得到渲染过的字符串。

### Laravel 的模板引擎 Blade

**特点：**

- 可以在视图中使用原生的 PHP 代码；
- 模板继承
- 区块


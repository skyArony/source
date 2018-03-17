---
title: 记一次完整的Linux环境配置
date: 2018-03-16 00:00:45
tags: 
- Linux
- 后端
- 运维
categories: 
- 后端
- Linux
thumbnail: 
---

刚学编程的时候，用腾讯云曾经尝试从头开始配置一次 Linux 环境，但当时卡在最后一步没有成功，又急着用就刷了云市场的镜像。最近打算把云主机重新装过一次，所以打算先用虚拟机完整的走一次流程。

**2018/3/17 更新**

这篇博客用较为简单的方法从头配置了常用开发环境，但是刚刚配置完、写完这篇博客我发现了更加简单的配置环境方法 —— [OneinStack](https://oneinstack.com/)。

对于想要稍微了解一下 Linux 各项系统设置的同学可以继续看这篇文章，回头再去了解 OneinStack。

对于只想要快速搭建系开发环境，又不想用云市场上老旧软件镜像的，建议了解 OneinStack ，可以看我另一篇文章 —— [用 OneinStack 快速搭建开 Linux 开发环境](/2018/03/17/用OneinStack快速搭建开Linux开发环境)


# 一、配置要求
除了系统使用云服务商提供的版本外，其他软件版本皆使用最新的版本（不要求最最新，至少不是上古版本）。这就导致了一个难点，本来用 `yum` 安装可以不用考虑包依赖的问题，但是要求软件版本皆为最新的话就一般得用编译安装，这样就会因为各种包之间的互相依赖问题而异常繁琐。

**福音：epel**

当我决定安装最新版软件的时候，就在想有没有哪些 yum 源，里面的软件就已经是最新的，后来发现真有，就是这个 `epel`，当然也不止这一个，还有许多其他的源。

```shell
# 安装 epel 源
yum install epel-release
```

一个推荐的社区[IUS](https://ius.io/GettingStarted/#install-via-automation)，提供新版的软件

```shell
# 下面两种方法都可以安装
yum -y install https://centos7.iuscommunity.org/ius-release.rpm

rpm -Uvh install https://centos7.iuscommunity.org/ius-release.rpm
```


# 二、系统软件版本记录
**时间**：2018-03-08

**系统**：CentOS-7-x86_64-Everything-1708 3.10.0-693.E17.x86_64

**软件**：

- Mysql 5.7.21
- Nginx 1.12.2
- PHP 7.2.2
- Python 3.6.4
- Git 1.8.3.1
- phpMyAdmin 4.7.9
    

# 三、虚拟机配置

## 1. 虚拟机选择
为了方便，直接选择了 Windows10 自带的虚拟机工具 —— Hyper-V。值得注意的是这个只有 Windows 10 专业版中才有，并且需要去控制面的程序和功能中去手动开启这项功能。具体怎么使用，网上都有教程就不在赘述。

## 2. 系统安装
[Hyper-V 安装CentOS 7](http://blog.csdn.net/chris_111x/article/details/52313797) —— 这篇文章相当详细，照着来。

另外自己安装 CentOS 才发现，CentOS 在安装时有一个“软件选择”选项，其中可以预先选择大多数的常用软件。

我开了两台虚拟机，一台选择了“基本网页服务器”、一台选择了“最小安装”。

**检查点：**

在系统装好之后可以手动设置一个检查点，这样就可以随时滚回到这个状态来测试系统了，省去了重新安装系统的时间。

**注意：**

> 安装虚拟机后，虚拟机可能没有网卡驱动，由于Hyper-v是不是支持linux网络服务的。

上面链接文档的这个地方，我碰到的网卡驱动正常，所以没有进行这一步。

## 3. 配置系统

### 3.1 配置网络
CentOS 7.0默认安装好之后是没有自动开启网络连接的！

[网络配置教程](http://blog.csdn.net/smart_ljh/article/details/51517232)，结合前面那一篇和这一篇互相对照就可以解决网络问题。

总结一下就是：

1. 配置虚拟交换机和旧版网络适配器
2. 配置 `ifcfg-eth0`，设置 `ONBOOT=yes`、`DNS1`、`DNS2` 等必要选项（所给链接里面用的静态IP，推荐动态）
3. ping www.baidu.com，通了的话就表示网络OK了

```shell
#进入网络配置文件目录
cd  /etc/sysconfig/network-scripts/ 

#编辑配置文件，添加修改以下内容
vi ifcfg-eth0
```

```
ONBOOT=yes  #开启自动启用网络连接

DNS1=8.8.8.8  #设置主DNS

DNS2=8.8.4.4  #设置备DNS
```

```
#保存退出
:wq!  

#重启网络
service network restart \ systemctl restart network

#测试网络是否正常
ping www.baidu.com  

#查看IP地址
ip addr  
```

### 3.2 设置主机名为www

```shell
hostname  www  #设置主机名为www

vi /etc/hostname #编辑配置文件

www   #修改localhost.localdomain为www

:wq!  #保存退出

vi /etc/hosts #编辑配置文件

127.0.0.1   localhost  www   #修改localhost.localdomain为www

:wq!  #保存退出

shutdown -r now  #重启系统
```

## 4. 安装常用系统软件

### 4.1 gcc
用于编译C。

### 4.2 wget
用于下载。

### 4.3 make
用于编译和安装。

### 4.4 net-tools \ iproute2 [操作](http://blog.jobbole.com/97270/)
iproute2的出现旨在从功能上取代net-tools

### 4.5 rz 和 sz
用于通过命令行下载和上传文件，包名为 `lrzsz`，`rz` 为上传， `sz` 为下载。

### 4.6 ftp 和 lftp
用于访问 ftp 服务器，`yum install ftp` 和 `yum install lftp` 安装。
 
### 4.7 vsftp
用户设置 ftp 账户，包名为 `vsftpd`，安装后设置开机启动然后启动。

## 5. 安装常用软件和环境

### 5.1 PHP
继上面添加 `epel` 源后

```shell
# 安装这个源，里面有 php7 等软件
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

# 安装php 7.2，PHP-FPM方式
yum install php72w-fpm php72w-opcache

# 或 安装php 7.2，mod_php方式，只能用于 Apache web服务器
yum install mod_php72w php72w-opcache

# 扩展的安装也异常简单
yum install php72w-xml

# 启动 php-fpm 并设置开机启动
systemctl start php-fpm
systemctl enable php-fpm
```

```
# 一口气装完所有扩展
yum install php72w-bcmath php72w-cli php72w-common php72w-dba php72w-devel php72w-embedded php72w-enchant php72w-fpm php72w-gd php72w-imap php72w-interbase php72w-intl php72w-ldap php72w-mbstring php72w-mysqlnd php72w-odbc php72w-pdo php72w-pdo_dblib php72w-pear php72w-pecl-apcu php72w-pecl-imagick php72w-pecl-mongodb php72w-pgsql php72w-phpdbg php72w-process php72w-pspell php72w-recode php72w-snmp php72w-soap php72w-tidy php72w-xml php72w-xmlrpc
```

更多可以参考[这里](https://webtatic.com/packages/php72/)。

### 5.2 Nginx

```shell
# 查看源中的 Nginx 版本
yum list nginx

# 我发现源中的是最新的稳定版，所以
yum install nginx

# 启动 Nginx 并设置开机启动
systemctl start nginx
systemctl enable nginx
```

修改配置文件：

```
server_name localhost;  # 原本为 server_name _;

location / {
    index index.php index.html index.htm;	# 原本没有这一行 
}

# 添加以下，配置 php-fpm
location ~ \.php$ {
    root           html;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```

网上找到的一个比较完整的配置文件说明：

```python
# nginx运行的用户名
user nginx;
# nginx启动进程,通常设置成和cpu的数量相等，这里为自动
worker_processes auto;

# errorlog文件位置
error_log /var/log/nginx/error.log;
# pid文件地址，记录了nginx的pid，方便进程管理
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
# 用来加载其他动态模块的配置
include /usr/share/nginx/modules/*.conf;

# 工作模式和连接数上限
events {
    # 每个worker_processes的最大并发链接数
    # 并发总数：worker_processes*worker_connections
    worker_connections 1024;
}

# 与提供http服务相关的一些配置参数类似的还有mail
http {
    # 设置日志的格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # access_log记录访问的用户、页面、浏览器、ip和其他的访问信息
    access_log  /var/log/nginx/access.log  main;

    # 这部分下面会单独解释
    # 设置nginx是否使用sendfile函数输出文件
    sendfile            on;
    # 数据包最大时发包(使用Nagle算法)
    tcp_nopush          on;
    # 立刻发送数据包(禁用Nagle算法)
    tcp_nodelay         on;
    # 链接超时时间
    keepalive_timeout   65;
    # 这个我也不清楚...
    types_hash_max_size 2048;

    # 引入文件扩展名与文件类型映射表
    include             /etc/nginx/mime.types;
    # 默认文件类型
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    # http服务上支持若干虚拟主机。
    # 每个虚拟主机一个对应的server配置项
    # 配置项里面包含该虚拟主机相关的配置。
    server {
        # 端口
        listen       80 default_server;
        listen       [::]:80 default_server;
        # 访问的域名
        server_name  _;
        # 默认网站根目录（www目录）
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.

        include /etc/nginx/default.d/*.conf;

        # 默认请求
        location / {
        }

        # 错误页(404)
        error_page 404 /404.html;
            location = /40x.html {
        }

        # 错误页(50X)
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```

**额外：nginx 子域名设置**

在 http 内 server 外添加一行

```
include vhosts/*.conf;
```

然后在 nginx 目录新建 vhost 目录，里面新建 example.conf（你设置自己的子域名时，最好改成文件中server_name加上'.conf'），写入以下：

```
server {
        listen       80;                        # IPV4 监听端口
        listen       [::]:80;                   # IPV6 监听端口
        server_name  blog.***.com;              # 绑定域名
        root         /data/www/blog.***.com;    # 网站根目录

        location / {
            index index.php index.html index.htm;	 
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

        # 设置php-fpm
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
    }
```

虽然不知道会不会保错，这个参考 `example.conf` 改成 `example.conf.bak` 最好。

### 5.3 Mysql
yum 的源中不知道为什么都没有 mysql 的包，必须先安装 mysql 的软件源

```shell
# 安装源，你可以去官网找适合你系统的最新 rpm 链接
rpm -Uvh https://repo.mysql.com//mysql57-community-release-el7-11.noarch.rpm

# 安装 mysql
yum install mysql-community-server

# 启动 mysql 服务
service mysqld start 
systemctl start mysqld.service # 对于支持这个命令的系统，这个命令更好

# 查看 mysql 服务的状态
service mysqld status
systemctl status mysqld.service # 对于支持这个命令的系统，这个命令更好

# 随意登录一次，提示登录失败，因为最新 mysql 默认使用随机密码
mysql -uroot -p

# 查看初始随机密码
grep 'temporary password' /var/log/mysqld.log

# 用查看到的随机密码登录并修改密码（包含大写、小写、数字和特殊字符8位以上）
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';

# 如果想去除麻烦的密码设定必须包含这些字符的限制
mysql> set global validate_password_policy=0;
mysql> set global validate_password_length=1;

# 设置开机启动，网上有用 chkconfig 的，是旧指令，用下面的即可
# 直接设置开机启动即可，不需要手动配置什么 .service 文件
systemctl start mysqld
systemctl enable mysqld
```

**额外：修改mysql数据库的存放路径**

一台服务器上存放的重要数据无外乎两种：数据库数据和网站数据。因此建议把数据库数据和网站数据放在一起，并单独放在一个挂载的盘中。
我个人习惯设置网站数据为目录 /data/www，因此就要把数据库文件目录设置到 /data/mysql。设置方法如下：

```
# 先停掉 mysql 服务
systemctl stop mysqld

# 找到文件 /etc/my.cnf
datadir=/var/lib/mysql    # 旧目录

# 前往旧目录把文件复制到你想要设置的新目录，并修改新目录用户和用户组
cp /var/lib/mysql /data/mysql
cd /data
chown -R mysql:mysql mysql

# 注释掉旧目录一行，设置新目录位置，socket 一行就别换了，不然还得额外设置一些东西
# datadir=/var/lib/mysql
datadir=/data/mysql

# 启动 mysql 服务
systemctl start mysqld
```

### 5.4 Python
Linux都是自带 Python 的，但是我们也可以安装最新的，不过要注意环境污染。

```shell
# 添加源
rpm -Uvh install https://centos7.iuscommunity.org/ius-release.rpm

# 安装 python
yum install python36u

# 配置虚拟环境
# Python3.3以上的版本通过venv模块原生支持虚拟环境，可以代替Python之前的virtualenv
# 选择一个你喜欢的路径
# 我也不知道为什么我这个安装后命令是 python3.6 ,和其他的有点不同
python3.6 -m venv py3

# 进入虚拟环境，进入后命令行最前面会出现（py3）
source py3/bin/activate

# 退出虚拟环境，经测试，任意路径都可以执行这个命令
deactivate
```

### 5.5 phpMyadmin
查看源中的版本，和官网的最新版本差了一点，所以这个打算下载安装，但是不同于 php 和 nginx，phpmyadmin 的安装不需要编译，只需要下载解压然后设置配置文件即可。

```
# cd 到 /data/www 目录下，你操作的时候去官网找最新下载链接
wget https://files.phpmyadmin.net/phpMyAdmin/4.7.9/phpMyAdmin-4.7.9-all-languages.zip

# 解压，默认没有 upzip，用 yum 安装一下，可以顺便把 zip 安装了
unzip phpMyAdmin-4.7.9-all-languages.zip

# 修改目录名
mv phpMyAdmin-4.7.9-all-languages phpmyadmin

# 复制一份配置文件，然后编辑
cd phpmyadmin
cp libraries/config.default.php config.inc.php  
```

**各个属性**

当然，我测试过了，默认不改任何东西也是OK的。默认登录密码为 mysql 数据库的账户名和密码。

```
$cfg['PmaAbsoluteUri']='http://localhost/phpmyadmin';
//将该参数的值设定为phpMyAdmin所处的位置。

$cfg['Servers'][$i]['host'] = 'localhost';
//设定MySQL所在的主机名或IP地址。如127.0.0.1这步很重要

$cfg['Servers'][$i]['auth_type'] = 'cookie';
// 设定进入phpMyAdmin管理MySQL的方式，共有三个选项：config、http、cookie。config是按配置文件登录；http是使用HTTP登录管理，在Apache模块安装下才能支持；cookie是最通用的管理方式。后两种方式的用户名和密码都是直接到数据库中验证。

$cfg['Servers'][$i]['user'] = 'root' ;
//设定管理MySQL的账号。

$cfg['Servers'][$i]['password'] = '' ;
//设定管理MySQL的密码。 

要是你想用空密码
把配置中的$cfg['Servers'][$i]['AllowNoPassword'] = false;
改成 $cfg['Servers'][$i]['AllowNoPassword'] = true;
```

**坑**

错误：

> phpMyAdmin - Error
Error during session start; please check your PHP and/or webserver log file and configure your PHP installation properly. Also ensure that cookies are enabled in your browser.

> session_start(): open(SESSION_FILE, O_RDWR) failed: No such file or directory (2)

> session_start(): Failed to read session data: files (path: /var/lib/php/session)

原因：
> php-fpm 还是 nginx 没有访问 session 的保存目录的权限

> 我具体没测试是 nginx 还是 php-fpm 了，应该是 php-fpm ，不过不重要，下面会把两者的启动用户设置为同一个

解决办法：

> 把 php-fpm 的启动账户设置成和 nginx 一样（nginx账户），后面一点有说怎么设置

> /var/lib/php/ 目录下新建 session 目录， 然后把 /var/lib/php 目录及子目录文件权限给 \etc\php-fpm.d\www.conf 中的 user 和 group 对应的用户和用户组。（即 nginx 和 php-fpm 的启动用户和启动组 —— nginx 账户）

> 你可能会问，为什么 session 的目录存在这？是在哪设置的？答案就在 www.conf 的最后面。


### 5.6 Java环境
还没学 Java 后台，学了再来补充。


## 6. 软件、目录和用户权限

### 6.1 nginx、mysql、php-fpm

启动这三个软件，分布运行如下命令，可以看到分布的进程权限用户所属：

```
ps -aux | grep nginx
# 父进程：root    子进程：nginx （在nginx.conf中有一行 “user nginx”）

ps -aux | grep mysql
# 父进程：root

ps -aux | grep php-fpm
# 父进程：root    子进程：apache
```

怎样改比较好呢？

建议：php-fpm 和 nginx 都运行在 nginx 用户下，同时网站目录 www 设置为 ftp 用户，nginx组，以后碰到要写入文件的地方，手动再把需要写入的目录设置组可写权限。

修改方式如下：

```
# 打开 /etc/php-fpm.d/www.conf，修改
user = nginx    # 原本是 apache
group = nginx   # 原本是 apache
```

然后新建一个 ftp ，即可用 ftp 账户来管理网站。

### 6.2 新建 FTP 账户

#### 6.2.1 vsftp 的安装和配置
先安装 `vsftp`，下面介绍一下配置文件。

有一个需要了解的地方：[FTP 的主动模式和被动模式](https://www.cnblogs.com/kuliuheng/p/3209744.html)

**1. /etc/vsftpd/vsftpd.conf**

主要的配置文件

```
# 使用本地时间
use_localtime=YES

# 支持被动模式，一定要设置为YES，不设置这个可能无法读取目录
pasv_enable=YES
# 设置被动模式的的连接端口，使用阿里云的同学请去后台开启对应端口
pasv_min_port=65400
pasv_max_port=65410

# 解决OOPS的问题
allow_writeable_chroot=YES

# 新建目录或文件的默认权限
local_umask=002  #（对应775）

# 只允许 /etc/passwd 中的的账号以实体用户方式登录
local_enable=YES

# 是否开启 chroot，两个一起作用
chroot_local_user=YES
chroot_list_enable=YES
# chroot_list_enable=YES 时才生效，此文件中的账户不会被 chroot，一行一个账号，需手动新建
chroot_list_file=/etc/vsftpd/chroot_list

# 可使用FTP服务的账户控制
# 开启这项功能
userlist_enable=YES
# YES时为黑名单，NO时为白名单
userlist_deny=NO
# 名单文件，一行一个账户
userlist_file=/etc/vsftpd/user_list

# 是否允许匿名登录，默认为 YES
anonymous_enable=NO

# 默认以 nobody 用户作为服务执行者，提高安全性
nopriv_user=nobody
```

以上的建议都设置了，没有的补上，不是的就修改。

> ①当chroot_list_enable=YES，chroot_local_user=YES时，在/etc/vsftpd.chroot_list文件中列出的用户，可以切换到其他目录；未在文件中列出的用户，不能切换到其他目录。
> ②当chroot_list_enable=YES，chroot_local_user=NO时，在/etc/vsftpd.chroot_list文件中列出的用户，不能切换到其他目录；未在文件中列出的用户，可以切换到其他目录。
> ③当chroot_list_enable=NO，chroot_local_user=YES时，所有的用户均不能切换到其他目录。
> ④当chroot_list_enable=NO，chroot_local_user=NO时，所有的用户均可以切换到其他目录。

**2. /etc/vsftpd/ftpusers**

列在这里面的用户是无法使用 ftp 的，里面已经包含了大多数的系统账户。同时也是你禁止一个账号 ftp 权限的最快方法。

#### 6.2.2 新建一个 ftp 账户

```
# 新建账户
useradd -d /data/www -s /sbin/nologin admin

# 更改密码
passwd admin
```

#### 6.2.3 测试连接

主动模式需要 20/21 端口，被动模式需要设置配置文件中几个 `pasv` 字段的属性。


## 7. 防火墙
CentOS7 中有 firewalld 和 iptables 两种防火墙，firewalld 可以看成是 iptables 的替代品，所以接下来主要介绍 firewalld。

我暂时没开启这服务，找了几个详细配置的文档。

[firewalld 详细设置1](https://havee.me/linux/2015-01/using-firewalls-on-centos-7.html)
[firewalld 详细设置2](http://blog.csdn.net/chris_111x/article/details/52313797)

```shell
# 查看状态
systemctl status firewalld

# 开启
systemctl start firewalld

# 开机启动
systemctl enable firewalld

# 开启端口：
# --zone 作用域 
# --add-port=80/tcp 添加端口，格式为：端口/通讯协议
# --permanent 永久生效，没有此参数重启后失效
firewall-cmd --zone=public --add-port=80/tcp --permanent

# 查看所有 zone 为 public 的端口
firewall-cmd --zone=dmz --list-ports

# 重启防火墙
firewall-cmd --reload
```


## 8. 几个小坑

**1. Hyper-V 新建虚拟机时有一个选择第几代的选项，请选择第一代，否则启动不了。**

**2. 阿里云有端口保护，需要用到端口时还要到阿里云后台去打开。**


# 四、常用命令和配置文件路径
## 1. 配置文件路径
数据库存放路径：/data/mysql
网站存放路径：/data/www
phpmyadmin 文件存放路径：/data/www/phpmyadmin
nginx 配置文件路径：/etc/nginx/nginx.conf
nginx 子域名配置文件： 
mysql 配置文件路径：/etc/my.cnf
php 配置文件路径：/etc/php.ini 
vsftpd 配置文件路径：/etc/vsftpd 目录

## 2. 常用服务重启
systemctl status\restart\start\stop mysqld\php-fpm\nginx\vsftpd

## 3. 日志文件
请看配置文件中设置的路径。
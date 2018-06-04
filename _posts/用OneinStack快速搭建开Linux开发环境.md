---
title: 用 OneinStack 快速搭建开 Linux 开发环境
date: 2018-03-17 15:51:39
tags:
- Linux
categories: 
- 后端
- Linux
thumbnail:
---

这篇博客将介绍目前我发现最简单的配置 Linux 开发环境的方法 ——[OneinStack](https://oneinstack.com/)。

对于想要稍微了解一下 Linux 各项系统设置的同学可以看我的另一篇文章 —— [记一次完整的Linux环境配置](/2018/03/16/记一次完整的Linux环境配置)，这篇文章记录了手动配置最新软件的 Linux 开发环境的方法。

# 一、OneinStack 介绍
OneinStack 是一个脚本，能够极度简便的安装服务器开发环境。直接看官网的介绍可能更明了一些。

- 持续不断更新
- 源码编译安装，大多数源码是最新stable版，并从官方网址下载
- 提供多个数据库版本(MySQL-5.7, MySQL-5.6, MySQL-5.5, MariaDB-10.1, MariaDB-10.0, MariaDB-5.5, Percona-5.7, Percona-5.6, Percona-5.5，AliSQL-5.6，PostgreSQL, MongoDB)
- 提供多个PHP版本(PHP-7.2,PHP-7.1,PHP-7.0,PHP-5.6, PHP-5.5,PHP-5.4,PHP-5.3)
- 提供Nginx、Tengine、OpenResty
- 提供多个Apache版本（Apache-2.4，Apache-2.2）
- 提供多个Tomcat版本（Tomcat-8，Tomcat-7，Tomcat-6）
- 提供多个JDK版本（JDK-1.8，JDK-1.7，JDK-1.6）
- 根据自己需求安装PHP缓存加速器，提供ZendOPcache、xcache、apcu、eAccelerator。及php加解密工具ionCube、ZendGuardLoader
- 根据自己需求安装Pureftpd、phpMyAdmin
- 根据自己需求安装Memcached、Redis
- jemalloc优化MySQL、Nginx
- 提供添加虚拟主机脚本
- 提供Nginx/Tengine/OpenResty/Apache、PHP、Redis、phpMyAdmin升级脚本
- 提供本地备、远程（服务器之间rsync）、腾讯云和又拍云备份脚本
- 提供CentOS 6、7下HHVM安装

# 二、开始
文档写的相当详细，所以我这以腾讯云上的 CentOS 7.4 环境为例，记录我所运行的 shell。

## 1. 先进行了我个人的部分习惯设置

```shell
# 安装 epel 源
yum install epel-release

# 安装提供新版软件的 IUS 源
yum -y install https://centos7.iuscommunity.org/ius-release.rpm

# 安装常用软件
yum install lrzsz    # 上传和下载命令
```

## 2. 按照 OneinStack 的官方文档操作

```
# 安装更新一波基本软件
yum -y install wget screen curl python

# 我这是腾讯云，下载源码和脚本
wget http://mirrors.linuxeye.com/oneinstack-full.tar.gz

# 解压
tar xzf oneinstack-full.tar.gz

# 如果你要修改各软件的默认安装目录，不过默认设置的都挺好的
cd oneinstack
vi options.conf

# 如果网路出现中断，可以执行命令`screen -R oneinstack`重新连接安装窗口，没有的话可以无视这个
screen -S oneinstack 

# 注：请勿sh install.sh或者bash install.sh这样执行
./install.sh 

# 然后安装官方文档的指示一步一步执行即可
```

### 2.1 安装完成

![](/用 OneinStack 快速搭建开 Linux 开发环境/安装完成.png)



## 3. 其他配置

### 3.1 添加附加组件

```shell
./addons.sh
```

里面有 composer 等其他附加组件的安装。



### 3.2 创建 FTP 虚拟用户账户

```shell
./pureftpd_vhost.sh
```

用来快捷创建 ftp 虚拟用户账户的脚本。



### 3.3 增删虚拟主机 

````shell
# 增加虚拟主机
./vhost.sh

# 删除虚拟主机
./vhost.sh del
````



### 3.4 备份和自动备份

```shell
# Set backup options 
./backup_setup.sh 

# Start backup, You can add cron jobs
./backup.sh 
# crontab -l # Examples 
0 1 * * * cd ~/oneinstack;./backup.sh  > /dev/null 2>&1 &
```



### 3.5 更新版本

```shell
./upgrade.sh
```



### 3.6 卸载

```shell
./uninstall.sh
```



## 4. 软件安装位置和配置文件位置

如果没有更改安装前的配置文件，那么安装完成后就如上图：

- 软件：/usr/local
- 数据： /data
- 日志：/data/wwwlogs
- 配置文件：/usr/local




**1. phpMyAdmin：http://x.x.x.x/phpMyAdmin**

**2. Opcache Control Panel URL：http://x.x.x.x/ocp.php**

​	可以加速php，但是PHP代码更新后，需要2～3分钟才能生效，可以打开进入这个页面点击 `reset`。



### 4.1 服务管理

Nginx/Tengine/OpenResty:

```
service nginx {start|stop|status|restart|reload|configtest}
```

MySQL/MariaDB/Percona:

```
service mysqld {start|stop|restart|reload|status}
```

PostgreSQL:

```
service postgresql {start|stop|restart|status}
```

MongoDB:

```
service mongod {start|stop|status|restart|reload}
```

PHP:

```
service php-fpm {start|stop|restart|reload|status}
```

HHVM:

```
service supervisord {start|stop|status|restart|reload}
```

**注**：hhvm进程交给supervisord管理，了解更多请访问《[Supervisor管理hhvm进程](https://blog.linuxeye.com/408.html)》
Apache:

```
service httpd {start|restart|stop}
```

Tomcat:

```
service tomcat {start|stop|status|restart}
```

Pure-Ftpd:

```
service pureftpd {start|stop|restart|status}
```

Redis:

```
service redis-server {start|stop|status|restart|reload}
```

Memcached:

```
service memcached {start|stop|status|restart|reload}
```



# 三、结束

我在使用这个脚本前，本来还在想会不会有什么地方会不符合个人操作习惯，但是整个跑一遍后发现，无论是安装路径的设置还是数据存储路径的设置，默认都已经设置的很好了，是在无话可说。

对应想要快速搭建服务器环境的朋友，强烈推荐这个工具。



# 四、更新

> 2018-3-23 更新
>
> 安装后我发现其中的 phpmyadmin 版本不是最新的，作为版本控必然是忍受不了的，记录更新步骤如下。

```
# 用 www 账户操作
# cd 到 /data/www 目录下，你操作的时候去官网找最新下载链接
wget https://files.phpmyadmin.net/phpMyAdmin/4.7.9/phpMyAdmin-4.7.9-all-languages.zip

# 解压，默认没有 upzip，用 yum 安装一下，可以顺便把 zip 安装了
unzip phpMyAdmin-4.7.9-all-languages.zip

# 修改目录名
mv phpMyAdmin-4.7.9-all-languages phpmyadmin

# 复制一份配置文件
cd phpmyadmin
cp libraries/config.default.php config.inc.php  
```


---
title: Laravel/Lumen教程（一）——基础环境搭建
date: 2017-12-30 18:18:57
tags:
- Laravel
- Lumen
- PHP框架
categories: 
- 后端
- PHP
- 框架
thumbnail: http://osv9x79o9.bkt.clouddn.com/17-12-30/62162735.jpg
---

网上搭建 Laravel/Lumen 环境的文章挺多的，但是一路操作下来还是很多坑，在此我整理了 Windows 和 Linux 下几种环境搭建方法。

# 一、 Windows篇

## 1. 下载最新版 phpstudy、mysql（压缩版）、PHPmyadmin
如果要用较新版本的 Laravel 或 Lumen ，对PHP以及其他软件的版本都有要求，然后 phpstudy 中mysql的版本较低，就需要手动来升级，当然如果所有环境都是手动搭建，没有使用 phpstudy 这样的工具可以忽略这里。

## 2. 更新 phpstudy 中的 mysql
1. 把 phpstud 目录下的 MYSQL 文件夹重命名备份，新建一个空MYSQL文件夹；
2. 把下载的压缩版 mysql ，解压到 MYSQL ，确保里面没有 data 文件夹，有的话删掉；
3. 以管理员权限进入其中的bin目录，执行 `mysqld --initialize` ，成功后执行 `mysqld -install` ,成功后再执行 `net start mysql` 进行启动测试；
4. `mysql -uroot -p` ，执行后要求输入密码，输入enter，提示错误，不用管；
5. 前往data文件夹找到err文件，记事本打开你们可以找到一个随机密码，复制随机密码（随机密码是 mysql5.7 以上才有的，以前的版本没有密码）;
6. 再次 `mysql -uroot -p` ，执行后输入刚刚复制的密码;
7. 修改密码，命令如下：
  ```
  // 两种方法，优先试第一个
  SET PASSWORD = PASSWORD('root');

  //或

  update mysql.user set authentication_string=password('root') where user='root' and Host = 'localhost';

  //原先改密码都是password列，但是5.7改成了authentication_string
  ```

## 3. 更新 phpmyadmin ，不更新的话进行数据库迁移时可能报编码方面的错误
直接下载最新版，和更新 mysql 一样备份、新建、粘贴即可。

## 4. 按上述操作存在的问题：
1. mysql 更新后一直常驻服务中，phpstudy的控制器不能控制其关闭，不影响使用，介意的可以自行寻找解决办法；

## 5. 安装 composer 环境
Windows 下建议使用[自动安装程序](http://docs.phpcomposer.com/00-intro.html#Installation-Windows)，会自动配置好环境变量，比较方便。配置国内镜像地址什么的就不多说了。

## 6. 安装 node.js 环境
这个在 Laravel 渲染页面时需要用到，只用Lumen的话可以先不装。

# 二、 Linux篇
Linux 下环境配置有多种选择：
- laradock（基于docker）
- 直接在本地搭建
- Homestead（基于虚拟机）

## 使用 docker + laradock 在 deepin 上搭建
### 1. 安装 docker
深度官方deepin已经集成了docker，不过不是docker-ce这样的最新版。要想使用最新版可以参考官网debian安装教程安装，不过由于深度在定制过程中进行了大量修改，所以使用官方教程是安装不成功的。具体需要几个地方进行调整。先说说安装步骤：

1.如果以前安装过老版本，可以先卸载以前版本
```
sudo apt-get remove docker docker-engine
```

2.安装docker-ce与密钥管理与下载相关的依赖库

```
sudo apt-get install apt-transport-https ca-certificates curl python-software-properties software-properties-common
```

3.下载并安装密钥
建议选用国内源
```
官方源：
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
国内源：
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -
```

4.添加源
```
官方源：
sudo add-apt-repository   "deb [arch=amd64] https://download.docker.com/linux/debian   wheezy   stable"

国内源：
sudo add-apt-repository   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian   wheezy   stable"
```

5.更新仓库缓存
```
sudo apt-get update
```

6.安装docker-ce
```
sudo apt-get install docker-ce
```

### 2. 安装docker-compose
```
pip install docker-compose
```

### 3. 安装laradock
1.首先将 Laradock 项目代码克隆到本地：
```
git clone https://github.com/Laradock/laradock.git
```
    
2.进入 laradock 目录将 env-example 重命名为 .env：
```
cp env-example .env
```
    
3.运行容器：
```
docker-compose up -d nginx mysql
```
如果指定端口已经被占用，运行上述命令会报错，关闭相应的服务再重新运行上述命令即可。
如果找不到相关进程，在`.env`文件和`docker-compose.yml`文件中把nginx所用的403端口改成其他不冲突的即可。

报错内容如下：
  >ERROR: for cd993bb246d5_laradock_nginx_1  Cannot start service nginx: driver failed programming external connectivity on endpoint cd993bb246d5_laradock_nginx_1 (7649ab2378acba94e996a87b242e44b4aa559cb3fa3223c53845d5fde233cd60): Error starting userland proxy: listen tcp 0.0.0.0:443: bind: address already in use

  >ERROR: for nginx  Cannot start service nginx: driver failed programming external connectivity on endpoint cd993bb246d5_laradock_nginx_1 (7649ab2378acba94e996a87b242e44b4aa559cb3fa3223c53845d5fde233cd60): Error starting userland proxy: listen tcp 0.0.0.0:443: bind: address already in use

  >ERROR: Encountered errors while bringing up the project.
    
4.进入容器并切换到laradock用户
```
docker-compose exec workspace bash
su laradock
```
直接以laradock身份进入会报错，我也不知道为什么

报错内容如下：
> Traceback (most recent call last):
  File "/usr/local/bin/docker-compose", line 11, in <module>
    sys.exit(main())
  File "/usr/local/lib/python2.7/dist-packages/compose/cli/main.py", line 68, in main
    command()
  File "/usr/local/lib/python2.7/dist-packages/compose/cli/main.py", line 121, in perform_command
    handler(command, command_options)
  File "/usr/local/lib/python2.7/dist-packages/compose/cli/main.py", line 417, in exec_command
    service = self.project.get_service(options['SERVICE'])
  File "/usr/local/lib/python2.7/dist-packages/compose/project.py", line 147, in get_service
    raise NoSuchService(name)
  File "/usr/local/lib/python2.7/dist-packages/compose/project.py", line 666, in __init__
    self.msg = "No such service: %s" % self.name
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe2 in position 0: ordinal not in range(128)

### 4. 安装 composer 和 node.js

参考本地环境搭建的步骤3 和 Windows 篇的步骤6。

## 直接在 Linux（deepin） 本地搭建环境
### 1. 安装LNMP 和 phpmyadmin
```
sudo apt install lnmp

sudo apt install phpmyadmin
```
设置软链接
```
sudo ln -s /usr/share/phpmyadmin /var/www/html
```

### 2. 配置nginx：
打开`/etc/nginx/sites-available`目录，修改`default`：

- 将`server_name _;`改成`server_name localhost;`
- 将其中php部分改成如下：

```
# pass PHP scripts to FastCGI server
#
location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	
	# With php-fpm (or other unix sockets):
	fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	# With php-cgi (or other tcp sockets):
	#fastcgi_pass 127.0.0.1:9000;
}
```

- 重启nginx
- 打开http://localhost/phpmyadmin，可以正常打开则配置完毕

### 3. 安装composer并设置到全局
安装
```
curl -sS https://getcomposer.org/installer | php

//or

php -r "readfile('https://getcomposer.org/installer');" | php
```

设置全局
```
sudo chmod a+x composer.phar
sudo mv composer.phar /usr/local/bin/composer
```
    
设置自动更新
```
sudo composer self-update
```
设置国内镜像源
```
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

## 用 Homestead（基于虚拟机）搭建环境
Homestead 环境我没有搭成功，坑比较多，这里给出一篇教程，感兴趣的可以按着来，下面我还会贴出我碰到的一些坑。

[Laravel 虚拟开发环境 Homestead](https://d.laravel-china.org/docs/5.5/homestead)。

### **坑1:** box文件的下载以及add
1.安装上面文档的指引，需要执行这个命令来下载box文件
```shell
vagrant box add laravel/homestead
```

>由于下载box的时候需要挂 VPN ，而且下载文件大小比较大，为了节省时间，所以在下载的时候，按 ctrl+c 取消，复制下载地址，格式如下：
https://atlas.hashicorp.com/laravel/boxes/homestead/versions/4.0.0/providers/virtualbox.box
其中版本改成执行命令后系统默认选择的版本

2.下载完之后放在任意一个英文目录下重命名为 homestead.box
3.在.box文件同目录下新建 metadata.json 文件
```json
{
  "name": "laravel/homestead",
  "versions": [{
    "version": "4.0.0",
    "providers": [{
      "name": "virtualbox",
      "url": "./homestead.box"
    }]
  }]
}
```
4.运行指令安装 box
```
vagrant box add metadata.json
```
5.查看安装的box
```
vagrant box list

laravel/homestead (virtualbox, 4.0.0)﻿​
```

### **坑2:** NFS
NFS是为了提高本机和虚拟机之间文件共享的效率，安装文档中也有提及。
但是但我们执行
```
vagrant up
```
提示错误：
> It appears your machine doesn't support NFS, or there is not an
adapter to enable NFS on this machine for Vagrant. Please verify
that \`nfsd\` is installed on your machine, and try again. If you're
on Windows, NFS isn't supported. If the problem persists, please
contact Vagrant support.

这是因为我们没有安装nfs工具，我用的是deepin，所以我执行
```
sudo apt install nfs-kernel-server
```
即可，其他系统改一下前面的包管理工具。

### **坑3:** NFS继续报错（卡在这不想继续弄了，感兴趣的可以继续）
在我输入`vagrant up`后，NFS居然又抱错了，如下：
>==> homestead-7: Mounting NFS shared folders...
The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!

>mount -o vers=3,udp,actimeo=1,nolock 192.168.10.1:/home/arony/Code/Lumen /home/vagrant/code/Lumen
result=$?
if test $result -eq 0; then
if test -x /sbin/initctl && command -v /sbin/init && /sbin/init 2>/dev/null --version | grep upstart; then
/sbin/initctl emit --no-wait vagrant-mounted MOUNTPOINT=/home/vagrant/code/Lumen
**……太占地方，省略**
mesg: ttyname failed: Inappropriate ioctl for device
mount.nfs: Connection timed out

# 三、 mac 篇
可以参考 Linux，或者使用 mac 独有的 [valet](https://d.laravel-china.org/docs/5.5/valet)。
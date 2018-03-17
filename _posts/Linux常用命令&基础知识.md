---
title: Linux常用命令&基础知识
date: 2018-02-13 00:44:40
tags:
- Linux
- 后端
- 运维
categories: 
- 后端
- Linux
thumbnail: 
---

Linux 使用过程中，经常有一些命令或者其他一些细节，有时候搜到了但是因为用的频率不高总忘。所以单独开一篇文章来纪录我经常忘的一些命令和细节，仅供自己快速翻阅，勿喷。

# 一、命令

## 1. 常用命令

### 1.1 搜索命令 —— grep [了解更多](http://man.linuxde.net/grep)

一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

使用样例：
```
// 从结果中搜
apt list | grep python

// 从文件中搜
grep match_pattern file_name

grep 'temporary password' /var/log/mysqld.log
```

### 1.2 md5sum & sha256sum
文件校验

```
// MD5校验
md5sum /path/filename

// SHA-256校验
sha256sum /path/filename
```

### 1.3 source ~/.bashrc
刷新全局变量的设置

```
source ~/.bashrc
```

### 1.4 安装软件
**.deb文件**

```
sudo dpkg -i 软件包名.deb
```

**.rmp文件**

```
// 转换为 .deb文件
sudo alien package_file.rpm
```

**.tar.gz文件**
把文件解压缩到你想安装的目录，然后在根目录下或者 `bin` 一般会有一个 `.sh` 文件用来安装。

### 1.5 who
查看当前有哪些用户在线。

### 1.6 ps -aux
查看当前所有任务。


### 1.7 service network restart \ systemctl restart network
重启网络服务。

### 1.8 ip addr
查看IP地址。

### 1.9 查找已安装的软件\文件位置

**rpm**

```shell
#查询已安装的以 mysql 开头的包
rpm  -qa mysql*

#查询已安装的 mysql 包
rpm -qa | grep mysql
```
  
**yum**

```shell
# 列出已经安装好的的包
yum list | grep installed
```

**whereis**

```shell
# 有时候可以借助查找文件的方式找到对应包的路径
whereis mysql
```

**find**

```shell
# find 查找路径 查找参数

# 在根目录下查找以.conf结尾的文件
find / -name *.conf
```

**locate**

```shell
locate 查找的文件
```

**ps**

```shell
# 可以通过查找进程的方法找到对应的包的路径
ps -ef|grep mysql

# 可以简写成
pgrep mysql
```


### 1.10 CentOS 的包管理 —— yum
软件的安装有 `rpm`、`yum` 和编译安装三种方法，`yum` 安装最为简单，因为它可以自动解决包之间的依赖关系。

**1.13.1 包的查找和安装**

```shell
# 从源中查找可使用的包
yum list 包名

yum search 包关键字 #这个的区别在于会匹配到所有含关键字的包

# 从源中安装指定的包
yum install 包名
```

**1.13.2 源的更换**

```shell
# 备份源
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo_bak

# 更改源
wget -O /etc/yum.repos.d/CentOS-Base.repo 源地址

# 清除缓存
yum clean all
yum makecache

# 查看设置好的源
yum repolist
```

### 1.11 pwd
查看当前所在路径。

### 1.12 设置开机启动，重启，启动 —— systemctl

| 任务 | 旧指令 | 新指令 | 
| ---  | --- | --- | 
| 使某服务自动启动 | chkconfig –level 3 httpd on | systemctl enable httpd.service | 
| 使某服务不自动启动 | chkconfig –level 3 httpd off | systemctl disable httpd.service | 
| 检查服务状态 | service httpd status | systemctl status httpd.service | 
| 显示所有已启动的服务 | chkconfig –list  | systemctl list-units –type=service | 
| 启动某服务 | service httpd start | systemctl start httpd.service | 
| 停止某服务 | service httpd stop | systemctl stop httpd.service | 
| 重启某服务 | service httpd restart | systemctl restart httpd.service | 

```shell
# 查看所有 systemd 服务
systemctl list-unit-files
```

---

## 2. 不常用命令

### 2.1 uname -r
查看内核版本。

### 2.2 date、cal
查看日期和日历。

### 2.3 shutdown、/sbin/poweroff、init 0
关机、重启。

```shell
# 关机
shutdown -h 
# 马上关机
shutdown -h now 

# 重启
shutdown -r
reboot
init 6
```

### 2.4 sync
将数据同步写入硬盘，避免重启或关机时的数据丢失。


# 二、技巧

## 1. 键位

### 1.1 Tab 键
按一下补齐命令，按两下显示可选择的命令或文件。

### 1.2 Ctrl + C
中止当前命令。

### 1.3 Ctrl + D
相当于 exit 命令。

---

## 2. 命令

### 2.1 man\info
`man + 你想要查询的命令` 就会显示说吗，按 `q` 可返回。`\stirng` 和 `?string` 分布向下和向上选择字符串。

### 2.2 /usr/share/doc 下存有众多软件包的说明文档


# 三、软件

## 1. 系统常用软件

### 1.1 gcc
用于编译C。

### 1.2 wget
wget 是一个从网络上自动下载文件的自由工具，支持通过 HTTP、HTTPS、FTP 三个最常见的 TCP/IP协议 下载，并可以使用 HTTP 代理。

### 1.3 make
用于编译和安装。

### 1.4 net-tools \ iproute2 [操作](http://blog.jobbole.com/97270/)
iproute2的出现旨在从功能上取代net-tools

### 1.5 rz 和 sz
用于通过命令行下载和上传文件，包名为 `lrzsz`，`rz` 为上传， `sz` 为下载。

### 1.6 ftp 和 lftp
用于访问 ftp 服务器，`yum install ftp` 和 `yum install lftp` 安装。
 
### 1.7 vsftp
用户设置 ftp 账户，包名为 `vsftpd`，安装后设置开机启动然后启动。


# 四、重要系统文件

## 1. 账户、组相关

三个重要文件：

**/etc/passwd**: 存储账户的文件。

**/etc/shadow**: 存储密码的文件。

**/etc/group**: 存储组的文件。

关于权限：

**-rwxrwxrwx**: 第一个字符的含意。

d —— 目录
\- —— 文件
l —— 连接文件
b —— 可供存储的接口设备
c —— 串行端口设备，例如键盘和鼠标

### 1.1 /etc/passwd 介绍
从其中取一行作为示例：

```
# ：作为分隔符，分成七个字段
root:x:0:0:root:/root:/bin/bash
```

七个字段分别为：

1. 账户名
2. 密码：在以前 UNIX 的时候，密码是存在这的，但后来为了安全改了，这里现在一般都是 x
3. UID：0 是系统管理员，1~499 是系统账号，其中 1~99 为系统自行创建的系统账号，100~499 为用户有系统账号需要时可以使用的UID，500~65535 为可登陆账号。
4. GID
5. 用户信息说明
6. 主文件夹
7. Shell：/bin/bash 表示能用 shell 登陆，/sbin/nologin 表示不能用 shell 登陆。

### 1.2 /etc/shadow 介绍
从中取几行作为示例：

```
# ：作为分隔符，分成九个字段
root:$6$XE6mMo/$ugMPYPDW9.hv7royLYKwKg0EkBBzhT2Od.SH0KpDTI7ixp5Z4kTGm:17599:0:99999:7:::
bin:*:17110:0:99999:7:::
daemon:*:17110:0:99999:7:::
```

1. 账号名称
2. 密码
3. 最近更动密码的日期：从1970年1月1日以来的天数。
4. 密码不可被更动的天数（与第3个字段相比）
5. 密码需要重新更改的天数（与第3个字段相比）
6. 密码需要更改期限前的警告天数（与第5个字段相比）
7. 密码过期后的账号宽限时间（密码失效日）（与第5个字段相比）：密码有效期为第三个字段 + 第五个字段。如果过期用户没有改密码，会在在用户登录的时候提醒更改密码之后才能操作，这段时间就是宽限时间。
8. 账号失效日期：账号失效日期，无论前面的密码字段是怎么设置的密码有效期，只要超过这个日期，账号就失效了。
9. 保留字段

### 1.3 /etc/group 介绍
依旧从文件中取几行来做示例：

```
# ：作为分隔符，分成四个字段
root:x:0:
bin:x:1:
daemon:x:2:
```

1. 用户组名称
2. 用户组密码：和前面一样，密码已经移动到 /etc/gshadow 去了，这里只有一个 x
3. GID
4. 此用户组支持的账号名称: 某个账号想要加入此用户组时，填入这个字段，多个账户用逗号隔开

**次要用户组和初始用户组**
初始用户组是创建账号时自动拥有的，不需要填入 /etc/group 的第四个字段。填入 /etc/group 的用户属于**次要用户组**。

**有效用户组**
那当一个用户属于两个用户组并新建了一个文件或者文件夹时，这个文件或文件夹的所属组到底是哪个？
这就涉及到**有效用户组**的概念了，通过 `groups` 命令可以列出当前用户的所属组，其中排在第一个的就是有效用户组，有效用户组会是当前用户新建文件或者文件夹的所属组。

**更改有效用户组**
如何更改有效用户组呢？可以使用 `newgrp` 命令，当前用户输入 `newgrp 所属的另一个用户组`，即可把当前用户组更改为所选择的用户组。
不过值得注意的是：这个是通过新建一个 Shell 来完成的，命令运行完后，你可以用 `exit` 回到先前的 Shell。

### 1.4 /etc/gshadow 介绍
依旧从文件中取几行来做示例：

```
# ：作为分隔符，分成四个字段
root:::
bin:::root,bin
utempter:!::
```

1. 用户组名
2. 密码列，如果为！表示为无合法密码，所以无用户组管理员
3. 用户组管理员账号
4. 用户组的所属账号

这个文件的主要作用是创建组管理员，例如有些只能使用 root 账户管理的事务，可能很多时候 root 账户比较忙碌，就可以添加一个用户组来解决这个问题。不过由于现在有 sudo 命名，所以这个组管理员的功能已经很少使用了。


# 五、常用操作

## 1. 账户、组操作

### 1.1 账户新建 —— useradd
通过这个命令可以新建一个账户，有以下这些常用参数：

1. [-d 目录绝对路径]：指定某个目录成为主文件夹；
2. [-r]：创建一个系统账户，系统账户默认不会创建用户文件夹；
3. [-s shell]： shell 可选 /bin/bash 和 /sbin/nologin

```
# 创建一个账户，新建的账户没有密码，还无法登录
useradd Test
```
运行上面的命令会创建一个叫做 `Test` 的账户，并创建一个 /home/Test 的用户主文件夹，权限为700.同时还会创建一个和用户名一样的组。

然后你查看 /etc/passwd 和 /etc/goup 你会发现，密码字段的一些属性，已经默认规定好了，这个默认属性是哪里来的呢？
答案就在 /etc/default/useradd 这个文件中。

### 1.2 账户密码修改 —— passwd
密码的修改有两种途径，一种是用 root 账户修改指定账户的密码，一种是账户登录后自己修改自己的密码。

```
# root 修改其他账户的密码
passwd Test

# 账户修改自己的密码，账户修改自己的密码时要先输入旧密码
passwd
```

### 1.3 账户删除 —— userdel
建议只有在账号真的确定不要使用的时候再删除，否则可以用各种方法让账户先失效。

```
# r 参数连同主文件夹一起删除
userdel -r username
```

一般账号使用一段时间后总会生成一些属于这个账号的文件，这些文件无法通过上面的命令删除，所以可以通过 `find / -user username` 找出来一一删除。

### 1.4 账户修改 —— chsh \ usermod
使用这个命令可以修改账户的可用shell，有两个参数。

```
# 列出系统可用的 shell
chsh -l

# 修改当前用户的 shell
chsh -s /sbin/nologin

# usermod 也可以
usermod -s /bin/bash username
```

## 2. 文件所属、权限操作

## 3. 文档新建操作

### 3.1 nano
打开一个文件，不存在则新建 —— nano
`F1` 打开帮助文档。

### 3.2 vi

### 3.3 touch


# 六、答疑解惑

### 1. `apt` 和 `apt-get` 有什么区别？ [了解更多](https://www.sysgeek.cn/apt-vs-apt-get/)

> 简单来说就是：apt = apt-get、apt-cache 和 apt-config 中最常用命令选项的集合。另外 apt 命令可以在某些操作时显示进度条。以上应该是最直观的解释。

而 `apt-cache` 和 `apt-config` 是低级但含有众多功能的命令，不过使用频率很低。

### 2. deepin 桌面：对一些安装后没有图标的软件如何新建一个图标
管理员权限打开 `/usr/share/applications` ,新建 `名字.desktop`,写入：
```
[Desktop Entry] 
Type=Application 
Name=Pycharm 
GenericName=Pycharm3 
Comment=Pycharm3:The Python IDE 
Exec=sh /home/sky/Application/pycharm-2017.3.3/bin/pycharm.sh   # 程序路径
Icon=/home/sky/Application/pycharm-2017.3.3/bin/pycharm.png     # 图标路径
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
```

### 3. Linux 下各目录文件的作用
/bin 二进制可执行命令 
/dev 设备特殊文件 
/etc 系统管理和配置文件 
/etc/rc.d 启动的配置文件和脚本 
/home 用户主目录的基点，比如用户user的主目录就是/home/user，可以用~user表示 
/lib 标准程序设计库，又叫动态链接共享库，作用类似windows里的.dll文件 
/sbin 系统管理命令，这里存放的是系统管理员使用的管理程序 
/tmp 公用的临时文件存储点 
/root 系统管理员的主目录（呵呵，特权阶级） 
/mnt 系统提供这个目录是让用户临时挂载其他的文件系统。 
/lost+found 这个目录平时是空的，系统非正常关机而留下“无家可归”的文件（windows下叫什么.chk）就在这里 
/proc 虚拟的目录，是系统内存的映射。可直接访问这个目录来获取系统信息。 
/var 某些大文件的溢出区，比方说各种服务的日志文件 
/usr 最庞大的目录，要用到的应用程序和文件几乎都在这个目录。其中包含： 
/usr/x11r6 存放x window的目录 
/usr/bin 众多的应用程序 
/usr/sbin 超级用户的一些管理程序 
/usr/doc linux文档 
/usr/include linux下开发和编译应用程序所需要的头文件 
/usr/lib 常用的动态链接库和软件包的配置文件 
/usr/man 帮助文档 
/usr/src 源代码，linux内核的源代码就放在/usr/src/linux里 
/usr/local/bin 本地增加的命令 
/usr/local/lib 本地增加的库
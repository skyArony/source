---
title: Linux随笔纪录
date: 2018-02-13 00:44:40
tags:
---

Linux 使用过程中，经常有一些命令或者其他一些细节，有时候搜到了但是因为用的频率不高总忘。所以单独开一篇文章来纪录我经常忘的一些命令和细节，仅供自己快速翻阅，勿喷。

# 一、命令

## 常用命令

### 1. 搜索命令 —— grep [了解更多](http://man.linuxde.net/grep)

一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

使用样例：
```
// 从结果中搜
apt list | grep python

// 从文件中搜
grep match_pattern file_name

grep 'temporary password' /var/log/mysqld.log
```

### 2. 文件校验 —— md5sum & sha256sum
```
// MD5校验
md5sum /path/filename

// SHA-256校验
sha256sum /path/filename
```

### 3. 刷新全局变量的设置 —— source ~/.bashrc
```
source ~/.bashrc
```

### 4. 安装软件
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

### 5. 打开一个文件，不存在则新建 —— nano
`F1` 打开帮助文档。

### 6. 查看当前有哪些用户在线 —— who

### 7. 查看当前所有任务 —— ps -aux

### 8. 修改当前用户的密码 —— passwd

### 9. 列出本目录的文件和文件夹 —— ls、ll
`ll` 等价于 `ls -l`。

### 10. 重启网络服务 —— service network restart \ systemctl restart network

### 11. 查看IP地址 —— ip addr

### 12. 查找已安装的软件\文件
这个值得多记点

**12.1 rpm**

```shell
#查询已安装的以 mysql 开头的包
rpm  -qa mysql*

#查询已安装的 mysql 包
rpm -qa | grep mysql
```
  
**12.2 yum**

```shell
# 列出已经安装好的的包
yum list | grep installed
```

**12.3 whereis**

```shell
# 有时候可以借助查找文件的方式找到对应包的路径
whereis mysql
```

**12.4 find**

```shell
# find 查找路径 查找参数

# 在根目录下查找以.conf结尾的文件
find / -name *.conf
```

**12.5 locate**

```shell
locate 查找的文件
```

**12.6 ps**

```shell
# 可以通过查找进程的方法找到对应的包的路径
ps -ef|grep mysql

# 可以简写成
pgrep mysql
```


### 13. CentOS 的包管理 —— yum
软件的安装有 `rpm`、`yum` 和编译安装三种方法，`yum` 安装最为简单，因为它可以自动解决包之间的依赖关系。

**13.1 包的查找和安装**

```shell
# 从源中查找可使用的包
yum list 包名

yum search 包关键字 #这个的区别在于会匹配到所有含关键字的包

# 从源中安装指定的包
yum install 包名
```

**13.2 源的更换**

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

### 14. 查看当前所在路径 —— pwd

### 15. 设置开机启动，重启，启动 —— systemctl

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

## 不常用命令

### 1. 查看内核版本 —— uname -r

### 2. 查看日期和日历 —— date、cal

### 3. 关机 —— shutdown、/sbin/poweroff、init 0

```shell
# 关机
shutdown -h 
# 马上关机
shutdown -h now 

# 重启
shutdown -r
```

### 4. 重启 —— reboot、init 6

### 5. 将数据同步写入硬盘 —— sync



# 二、技巧

## 键位

### 1. Tab 键
按一下补齐命令，按两下显示可选择的命令或文件。

### 2. Ctrl + C
中止当前命令。

### 3. Ctrl + D
相当于 exit 命令。

---

## 命令

### 1. man\info
`man + 你想要查询的命令` 就会显示说吗，按 `q` 可返回。`\stirng` 和 `?string` 分布向下和向上选择字符串。

### 2. /usr/share/doc 下存有众多软件包的说明文档


# 三、疑问

### 1. `apt` 和 `apt-get` 用什么区别？ [了解更多](https://www.sysgeek.cn/apt-vs-apt-get/)

> 简单来说就是：apt = apt-get、apt-cache 和 apt-config 中最常用命令选项的集合。另外 apt 命令可以在某些操作时显示进度条。以上应该是最直观的解释。

而 `apt-cache` 和 `apt-config` 是低级但含有众多功能的命令，不过使用频率很低。

### 2. 对一些安装后没有图标的软件如何新建一个图标
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

# 四、常用软件

### 1. wget
wget 是一个从网络上自动下载文件的自由工具，支持通过 HTTP、HTTPS、FTP 三个最常见的 TCP/IP协议 下载，并可以使用 HTTP 代理。









### 2. php
PHP常用命令

```shell
# 查看PHP版本
php -v

# 查看PHP扩展
php -m
```
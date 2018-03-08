---
title: Linux随笔纪录
date: 2018-02-13 00:44:40
tags:
---

Linux 使用过程中，经常有一些命令或者其他一些细节，有时候搜到了但是因为用的频率不高总忘。所以单独开一篇文章来纪录我经常忘的一些命令和细节，仅供自己快速翻阅，勿喷。

## 命令

### 1. 搜索命令 —— grep [了解更多](http://man.linuxde.net/grep)

一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

使用样例：
```
// 从结果中搜
apt list | grep python

// 从文件中搜
grep match_pattern file_name
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

## 疑问

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



## 技巧
---
title: 关于Hexo
tags:
  - 杂文
  - Hexo
categories:
  - 杂文
date: 2018-03-24 00:10:17
thumbnail:
---

用 Hexo 来部署博客是真的很爽，但是用起来还稍微有点小问题，具体看下面。



# 一、部署方式

部署方式有两种：

1. 本地安装 Hexo → hexo g -d 部署到 VPS 或 GitHub Page → 用户访问
2. VPS 上安装 Hexo → 本地编写 Markdown → 同步到 VPS 对应目录 → hexo g 解析成静态页（此时还可以选择把静态页同步到其他诸如 GitHub Page 的服务） →  hexo s 对外服务 → 用户访问

**对于第一种方式**

一般大家都是用的这种方式，但是这种方式有一个问题，就是你有多台工作机时，得在几台机器上都装上 Hexo 才能实现每台机器都能部署。

**对于第二种方式**

这个方式用的人很少，但是可以解决第一种方式的问题，但是比较麻烦的就是设置，比起第一种方法要额外多很多设置的地方，如果要部署到 GitHub Page 更麻烦，哪天有空，我会把这种方式走一遍整理一下。



# 二、多工作机的文档同步

这个有个解决办法是把 Hexo 目录下的 source 目录新建作为一个 Git 项目，然后同步到 GitHub，以此管理就可以了。



# 三、Hexo 该如何备份

一般来说备份 scaffolds（模板）、source（文档）、theme（主题及设置）、_config.yml（hexo设置）即可。然后在新环境下：

```shell
# 全局安装hexo
npm install hexo-cli -g

# 安装部署工具
npm install --save hexo-deployer-git

# 安装搜索引擎，如果你用的是这个的话
npm install --save hexo-generator-search

# 把备份的文件复制进去即可
```



# 四、Hexo 的 Git 配置

**_config.yml 设置（hexo的，不是主题的），三行一个单元，可以配置多个**

```yml
deploy:
- type: git
  repo: hexo@yfree.cc:/home/hexo/hexo.git
  branch: master
```

**VPS 上新建一个用户**

```shell
# 用户目录下新建 .ssh 目录，新建 authorized_keys 文件

# 用户目录下新建 hexo.git
cd ~
git init --bare hexo.git

# hexo.git 目录下的 hooks 目录，新建 post-receive 文件，写入：
#!/bin/sh
git --work-tree=/data/wwwroot/blog.yfree.cc --git-dir=/home/hexo/hexo.git checkout -f

# 添加可执行权限
chmod +x post-receive

# 新建一对 ssh 密钥，发送公钥到 VPS
ssh-copy-id -i ~/.ssh/hexo.pub 'git@vps地址' -p 'ssh端口'

# 如果报错，把本地的 .ssh 文件夹中的 known_hosts 文件中的对应行删掉即可
```


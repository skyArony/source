---
title: 快查-Git
date: 2017-07-29 22:31:15
tags: 
- Git
- 快查
categories: 
- 快查
- Git
---

Git 是管理代码等类文本文件版本最为方便的工具，学会 Git 会让你在版本管理方便省下许多功夫。

本文整理了一些常用的命令，便于自己快速查阅。

**参考资料：**

1. [Git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
2. [Git简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
3. [handbook-git](http://wangchujiang.com/handbook/html/git.html)
4. [Git的奇技淫巧](https://github.com/521xueweihan/git-tips)



# 一、完整流程

## 1. 下载 git

一路点确定安装下去，先不要琢磨每个选项是什么用处，默认就好，以后再次安装时再看自然就明白了。



## 2. 相关配置

安装好后第一步，就是先配置用户名和邮箱

### 修改用户名和地址

```shell
git config --global user.name "your name"
git config --global user.email "your email"
```

### 查看用户名和地址

```shell
git config user.name
git config user.email
git config --list			# 查看信息的配置信息
```



## 3. 配置SSH

若要与远程git服务器进行数据交互，大部分git服务都可以选择git方式和HTTPS方式，而git方式通过配置ssh可以更加方便的使用，所以接下来配置ssh

### 生成SSH key

`ssh-keygen -t rsa -C "你的邮箱"`   

- `-t`是指定要创建的密钥类型，可以使用："rsa1"(SSH-1) "rsa"(SSH-2) "dsa"(SSH-2)。
- `-C`后面加上的是注释
- 生成密钥是第一个输入项是密钥的名字，第二个输入项`empty for no passphrase`留空并确认可达到登录时无需输入密码的效果

`ssh-add ~/.ssh/id_rsa`然后添加新生成的私钥，如果提示`Could not open a connection to your authentication agent.`,那么执行以下命令，然后再执行一次：

```shell
eval `ssh-agent -s`
```

### 用config管理多个密钥

在`.ssh`目录下新建config文件，写入

```shell
#github【名称】
Host github.com【主机地址】
HostName github.com【主机名】
User git【用户名】
IdentityFile ~/.ssh/github【私钥路径】
```

以上内容为一组，写入保存后即可用 `ssh github.com` 来进行连接测试。

### 发送公钥到 VPS 或 Git服务商

```shell
# 可以手动复制，也可以用这个命令发送过去
ssh-copy-id -i ~/.ssh/id_rsa.pub 'git@vps地址' -p 'ssh端口'

# 如果报错，把本地的 .ssh 文件夹中的 known_hosts 文件中的对应行删掉即可
```

### SSH连接测试

```shell
ssh -T git@"git服务器地址"
```

**注：**

- 创建成功后务必进行一次ssh测试
- Windows下，ssh密钥的目录在~/.ssh,生成文件中带`.pub`后缀的是公钥，add到Gitlab或GitHub的SSH配置中
- 有时候还是会莫名其妙报错，去 `known_hosts` 中删除掉一些不用的远程服务器 key 即可



## 4. 新建 & 克隆

```shell
git init			# 新建
git clone 地址	   # 克隆	
```



# 二、常用命令

## 1. 初始化

```shell
git clone 地址	# 复制一个远程仓库
git init	# 自己新建一个仓库
```

## 2. 远程

```shell
git remote add origin git@jslite.github.com:JSLite/JSLite.git # 添加远程路径
git remote set-url --add origin https://gitlab.com/wang/JSLite.js.git  # 再增加一个远程地址
git remote set-url --delete origin https://oschina.net/wang/JSLite.js.git  # 删除一个远程地址
git remote -v					# 查看远程库信息
git remote remove origin		 # 删除该远程路径
```

## 3. 拉取 & 推送

```shell
git pull									  # 拉取，拉取只会拉取一个地址的内容
git pull origin remotebranch:localbranch 		 # 拉取远程分支到本地分支

git add <filename> 							   # 添加文件
git add .									  # 添加所有文件
git commit -m  "提交信息"						# 提交到本地分支
git push -u origin master 						# -u 指定默认主机，以后直接 git push
git push -f 									# 推送（强制）
```

> git push <远程主机名> <本地分支名>:<远程分支名>

## 4. 分支管理

### 新建

```shell
git branch test			 # 新建 test 分支
git checkout dev		 # 切换到指定分支
git checkout -b dev 	  # 创建并同时切换到 dev 分支 
git checkout -b dev origin/dev 	# 基于远程 dev 分支创建本地 dev 分支并设置追踪
git branch -m old new		# 重命名分支
```

### 查看

```shell
git branch			# 查看所有分支，显示结果中前面带*号的为当前分支
git branch -a 		# 查看已知的所有分支（本地和远程）
```

### 重命名

```shell
git branch -m newname		# 重命名当前分支
```

### 合并

```shell
git merge origin/master 	# 在本地分支上合并远程分支
git rebase origin/master 	# 在本地分支上合并远程分支
git merge branchName		# 将 branchName 分支合并到当前分支
```

### 删除

```shell
git branch -d <name>	# 删除一个分支
git branch -D <name>	# 强行删除一个分支
git push origin :remotebranch	# 删除一个远程指定分支
```

### 连接绑定

```shell
git branch --set-upstream-to=origin/dev dev	# 将本地 dev 分支与远程 dev 分支之间建立链接
```



## 5. 版本管理

### 查看

```shell
git log    		# 可查看当前版本的所有提交记录（不显示回退后未来的提交记录）
git reflog		# 可查看版本库建立以来所有的提交记录
git status		# 获取当前的状态，非常有用，因为git会提示接下来的能做的操作
```

### 解决冲突

```shell
git diff file 			# 查看指定文件的差异
git diff --stat 		# 查看简单的diff结果
git diff 			    # 比较Worktree和Index之间的差异
git diff --cached 		# 比较Index和HEAD之间的差异
git diff HEAD 			# 比较Worktree和HEAD之间的差异
git diff branch 		# 比较Worktree和branch之间的差异
git diff branch1 branch2 # 比较两次分支之间的差异
git diff commit commit 	 # 比较两次提交之间的差异
```

### 撤销修改

commit_id 可以从以前的提交记录或者用 `git log` 和 `git relog` 命令得到，还可以用 `HEAD^` 以及 `HEAD^^` 表示上一个版本或上两个版本，用 `HEAD~100` 表示第上100个版本。

```shell
git reset --hard commit_id 
```



# 三、常用操作

##1. 强制用远程覆盖本地

```shell
git fetch --all 				   # 只是下载代码到本地，不进行合并操作
git reset --hard origin/master  	# 把HEAD指向最新下载的版本
```



## 2. 远程分支与本地分支

### 远程有 master 和 dev

要在本地创建 dev 并和远程 dev 关联起来

```shell
git checkout -b localbranch     			# 新建并切换到本地分支
git pull origin remotebranch:localbranch 	 # 本地分支与远程分支相关联
```

### 远程只有 master

要在本地创建 test 并推送到远程

```shell
git checkout -b localbranch   				# 新建并切换到本地分支
git push origin localbranch:remotebranch   	 # 这样远程仓库中也就创建了一个分支
```

### 查看远程分支和本地分支之间的追踪关系

```shell
git branch -vv
```



## 3. 文件忽略

```shell
touch .gitignore  					# 新建 .gitignore 文件
echo node_modules/ >> .gitignore	 # 添加文件到忽略列表
```

编写忽略规则，格式如下：

```shell
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa

# 加上感叹号表示不能忽略
!README.md
```



## 4. tag 管理

### 新建标签

- `git tag <name>` 新增一个标签，默认打到最新的commit上
- `git tag v0.9 6224937` 将标签打到指定的commit上
- `git tag -a <tagname> -m "blablabla..."` -a后加标签名，-m后加标签信息

### 查看标签

- `git tag` 查看所有标签
- `git show <tagname> ` 查看改标签的详细信息
- `git checkout tagname`  切换到标签

### 推动标签

- `git push origin <tagname>` 可以推送一个本地标签；
- `git push origin --tags ` 可以推送全部未推送过的本地标签；

### 删除标签

- `git tag -d <tagname> ` 可以删除一个本地标签；
- `git push origin :refs/tags/<tagname> ` 可以删除一个远程标签。

### 获取远程标签

- `git pull --all`  获取远程所有内容包括tag



## 5. 分支管理策略

实际开发时，我们应该遵循一定的开发策略，以便于进行管理和开发。

> 首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
> 那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；
> 你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以 了。

所以，团队合作的分支看起来就像这样：
![git分支管理策略](http://upload-images.jianshu.io/upload_images/2662764-a3f4de914acf6e2b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

```
git merge --no-ff -m "merge with no-ff" dev
```

### bug 分支

一般修复bug会另外开一个专门的bug分支来进行修复，但是如果现在正在分支上进行工作，接到紧急任务需要修复一个bug就会出现一个冲突：工作区是被占用的。
此时可以用`git stash`命令来储存当前工作现场

- `git stash`储存当前工作区
- `git stash list`列出当前已经储存了的工作区
- `git stash apply <stash@{0}>`恢复储存的工作区，此种恢复，不会删除存储的工作区，可以在后面加工作区名，表示要恢复到哪个工作区
- `git stash drop`用来删除指定工作区
- `git stash pop`用来恢复到一个工作区，并删除工作区记录

### feature 分支

用来开发新的功能

### 多人协作

因此，多人协作的工作模式通常是这样：

首先，可以试图用`git push origin <本地分支名>`推送自己的修改；

如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；

如果合并有冲突，则解决冲突，并在本地提交；

没有冲突或者解决掉冲突后，再用`git push origin <本地分支名>`推送就能成功！

如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建

用命令`git branch --set-upstream branch-name origin/branch-name`创建本地和远程的分支链接

这就是多人协作的工作模式，一旦熟悉了，就非常简单。



## 6. 撤销修改

git中版本管理分成三个区域：工作区、暂存区和分支
![git版本管理](http://upload-images.jianshu.io/upload_images/2662764-fb39294c2190f423.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里引用一段关于解释这个三个区域关系的话：

> 1).从工作目录，提交到stage。
> 2).从stage提交到master。
>
> 从工作目录提交到stage，需要用add或者rm命令，只提交到stage，而没有提交到master，是不会自动同步到master的。
>
> 从stage提交到master用commit命令。
>
> 退回也是要分两步，一个是从master退回到stage，然后再从stage退回到工作目录。
>
> 对于还没有提交到stage的，可以从stage用checkout命令退回，这一步会取stage中的文件状态，覆盖掉工作目录中文件的状态，跟master完全没关系。
>
> 对于已经到达stage的，想把state中的文件状态用master中的覆盖掉，就用reset命令，这样就把stage中修改用master的状态覆盖掉了，完全跟工作目录没关系

针对这三个区域的撤销修改如下：

- 工作区：`git checkout -- <filename>` 此命令可以将工作区的文件修改成上一次`add`或`commit`的状态
- 暂存区：`git reset HEAD <filename>` 通过此命令暂存区的版本回退至想要的版本，然后再用上面工作区的撤销修改命令将工作区文件进行撤销修改
- 分支： 参考上面版本回退命令，直接将版本回退至指定版本，然后工作区的文件也会随之改变



## 7. 自定义git

```shell
git config --global alias.st status
git config --global alias.br branch
git config --global alias.co checkout
git config --global alias.ci commit
```

配置好后再输入git命令的时候就不用再输入一大段了，例如我们要查看状态，只需：

```shell
git st
```



## 8. 把 fork 分支更新

有时候，我们把一个项目 fork 到了自己的库中，但是源项目更新比较频繁，这时候我们就像要把源更新拉取过来，合并到自己的里面，怎么操作呢？

### 不用命令行

参考知乎上 [**pezy** 的回答](https://www.zhihu.com/question/20393785/answer/30725725)

### 命令行方法

当然，那是完全不用命令行的办法，其实我还是更推荐命令行，流程如下：

1. 首先要先确定一下是否建立了主repo的远程源：

   ```
   git remote -v
   ```

2. 如果里面只能看到你自己的两个源(fetch 和 push)，那就需要添加主repo的源：

   ```
   git remote add upstream URL
   git remote -v
   ```

   然后你就能看到upstream了。

3. 如果想与主repo合并：

   ​

   ```
   git fetch upstream
   git merge upstream/master
   ```



# 四、常见问题

## 1. Git忽略规则及.gitignore规则不生效的解决办法

有时候添加顾略规则会发现并不生效，原因是 .gitignore 只能忽略那些原来没有被 track 的文件，如果某些文件已经被纳入了版本管理中，则修改 .gitignore 是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：

```shell
git rm -r --cached .
git add .
git commit -m 'update .gitignore'

git rm --cached *	# 取消一个文件的跟踪
```

解决方法来自：http://www.pfeng.org/archives/840



## 2. 密码不小心提交到了远程分支

当密码提交到远程分支以后，如果后来把文件加入忽略列表，但是以前的commit中还会留有密码文件。这个时候就得用到核弹级命令`git filter-branch`，之所以叫核弹级，是因为这个命令十分危险，理论上是可以对你的分支做任何操作。建议先拉一个分支再进行操作。
使用方法：

1. 首先关闭远程分支的分支保护；
2. 然后执行

```shell
# 这里是删除 分支中所有的 passwords.txt 文件
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD --all

# --env-filte 表示修改邮箱环境
# --msg-filter 表示修改提交信息
# --tree-filter 表示修改文件列表
```

3. 接着

```shell
git push origin master --force
```

4. 最后别忘了把远程分支又改回保护状态。



## 3. 修改commit的信息

- `git commit --amend` 如果没有进行新的提交
- `git reset --soft xxx` 如果进行了新的提交，xxx是提交ID，但是会把后面的提交都合并为一次，因此最好不要这么使用
- `git rebase -i HEAD~3`  修改最近三次的提交说明，或者其中任意一次
- `git rebase --continue` 修改提交说明，退出编辑器


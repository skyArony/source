---
title: Git快查笔记
date: 2017-07-29 22:31:15
tags: 
- git
- 学习笔记
- 整理
categories: 
- 学习笔记
- git
---

# 一、楔子
Git 是管理代码等类文本文件版本最为方便的工具，学会 Git 会让你在版本管理方便省下许多功夫

如果要既简要又系统的学习 Git，廖雪峰前辈的教程非常不错，值得一看：[git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000),然后还有这个[git简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)。

既然已经有如此优秀的教程，那本文的用意便在于记录下使用频率特别高的命令和其中一些特别需要注意的点，方便以后快捷查看。

# 二、常用命令

## 1. 初始化
- `cd`到指定目录然后`git init`
- `git clone`项目地址

## 2. 状态查看
- `git status` 查看当前的提交状态
- `git diff <filename>` 对比文件的修改

## 3. 提交（每次提交前都需要add和commit）
- `git add <filename>` 添加文件到暂存区，`git add .`用于添加所有文件
- `git commit -m  "提交信息"` 提交暂存区中的文件

## 4. 版本回退
- `git reset --hard commit_id` commit_id可以从以前的提交记录或者用`git log`和`git relog`命令得到，还可以用`HEAD^`以及`HEAD^^`表示上一个版本或上两个版本，用`HEAD~100`表示第上100个版本
- `git log`可查看当前版本的所有提交记录（不显示回退后未来的提交记录）
- `git reflog`可查看版本库建立以来所有的提交记录

## 5. 撤销修改
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

## 6. 删除
- `git rm <filename>` 删除工作区的文件并将这个删除的修改操作`add`到暂存区

## 7. 远程仓库
远程仓库的用法一般是：一台服务器，多台客户机，首次使用时用`git clone`从服务器复制git库，每次开发时再用`git pull`从服务器取到最新版本，开发结束后用`git push`上传到服务器
1. 推送到远程`git push`
远程库是空的时，第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
  - `git remote add origin git@server-name:path/repo-name.git`使用这个命令第一次关联远程库
  - `git push -u origin master`第一次推送master分支的所有内容
  - `git push origin master`推送最新修改
2. 从远程克隆`git clone`
Git支持多种协议，默认的`git://`使用ssh，但也可以使用https等其他协议。使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放`http`端口的公司内部就无法使用`ssh`协议而只能用`https`。
要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone命令`克隆。
Git支持多种协议，包括`https`，但通过`ssh`支持的原生`git`协议速度最快。
  - `git clone git@github.com:michaelliao/gitskills.git` 使用示例

## 8. 分支管理
### 8.1 创建、合并和删除分支
- `git checkout -b dev`创建并切换到`dev`分支，其中`-b`分支表示创建并切换，相当于以下两条命令、
```
git branch dev
git checkout dev
```
- `git checkout <name>`切换到指定分支
- `git branch`此命令可以列出所有分支，显示结果中前面带`*`号的为当前分支
- `git branch <name>`表示创建新分支
- `git branch -d <name>`删除指定分支
- `git branch -D <name>`强行删除一个分支
- `git branch -a` 查看已知的所有分支（本地和远程）
- `git merge <name>`合并某分支到当前分支

### 8.2 解决冲突
合并分支时，若分支之间存在冲突，那么就需要先解决冲突然后再进行合并


### 8.3 强制覆盖本地，使用网络上的新状态

```
git fetch --all //只是下载代码到本地，不进行合并操作
git reset --hard origin/master  //把HEAD指向最新下载的版本
```

### 8.4 分支管理策略
实际开发时，我们应该遵循一定的开发策略，以便于进行管理和开发。

> 首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；
你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以 了。

所以，团队合作的分支看起来就像这样：
![git分支管理策略](http://upload-images.jianshu.io/upload_images/2662764-a3f4de914acf6e2b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。
```
git merge --no-ff -m "merge with no-ff" dev
```

### 8.5 bug分支
一般修复bug会另外开一个专门的bug分支来进行修复，但是如果现在正在分支上进行工作，接到紧急任务需要修复一个bug就会出现一个冲突：工作区是被占用的。
此时可以用`git stash`命令来储存当前工作现场
- `git stash`储存当前工作区
- `git stash list`列出当前已经储存了的工作区
- `git stash apply <stash@{0}>`恢复储存的工作区，此种恢复，不会删除存储的工作区，可以在后面加工作区名，表示要恢复到哪个工作区
- `git stash drop`用来删除指定工作区
- `git stash pop`用来恢复到一个工作区，并删除工作区记录

### 8.6 feature分支
用来开发新的功能

### 8.7 多人协作
因此，多人协作的工作模式通常是这样：

首先，可以试图用`git push origin <本地分支名>`推送自己的修改；

如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；

如果合并有冲突，则解决冲突，并在本地提交；

没有冲突或者解决掉冲突后，再用`git push origin <本地分支名>`推送就能成功！

如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建

用命令`git branch --set-upstream branch-name origin/branch-name`创建本地和远程的分支链接

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

- `git remote -v`查看远程库信息

### 8.9 远程分支与本地分支
**github上已经有`master`分支 和`dev`分支**

要在本地创建`dev`并和远程dev关联起来
```git
git checkout -b dev #新建并切换到本地dev分支
git pull origin dev #本地分支与远程分支相关联
```

**github上只有`master`分支**

要在本地创建`test`并推送到远程
```git
git checkout -b test   #新建并切换到本地test分支
git push origin test   #这样远程仓库中也就创建了一个test分支
```
**git push**  
```git
git push <远程主机名> <本地分支名>:<远程分支名>
git push -u origin master #参数带-u表示指定一个默认主机，以后就可以直接git push
```

### 8.10 查看远程分支和本地分支之间的追踪关系

```
git branch -vv
git config --list
```

## 9. 标签管理
在git中，标签用于方便人的记忆，和某个commit绑定在一起，就像版本号（V1.0）一样，切换到分支后即可打标签
- `git tag <name>`新增一个标签，默认打到最新的commit上
- `git tag`查看所有标签
- `git tag v0.9 6224937`将标签打到指定的commit上
- `git show <tagname>`查看改标签的详细信息
- `git tag -a <tagname> -m "blablabla..."`-a后加标签名，-m后加标签信息
- `git push origin <tagname>`可以推送一个本地标签；
- `git push origin --tags`可以推送全部未推送过的本地标签；
- `git tag -d <tagname>`可以删除一个本地标签；
- `git push origin :refs/tags/<tagname>`可以删除一个远程标签。

## 10. 自定义git
`git config --global color.ui true`让git命令行显示颜色

## 11. 文件忽略
- `touch .gitignore`创建`.gitignore`文件
- `.gitignore`文件中写需要忽略的文件，然后提交上去

格式如下：
```
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

## 12. 配置别名
`--global`参数加上时表示全局，对本电脑下所有git仓库都有用，不加则只对当前仓库起作用
- `git config --global alias.st status` 给`status`起别名`st`
- 给这一长串命令起别名`lg`
```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`
```

# 三、完整走一次流程
## 1. 先去git官网下载最新版git，
一路点确定安装下去，先不要琢磨每个选项是什么用处，默认就好，以后再次安装时再看自然就明白了。

## 2. 配置和查看用户名
安装好后第一步，就是先配置用户名和邮箱
### 2.1 修改用户名和地址
```git
git config --global user.name "your name"
git config --global user.email "your email"
```
### 2.2 查看用户名和地址
```git
git config user.name
git config user.email
```

## 3. 配置SSH
若要与远程git服务器进行数据交互，大部分git服务都可以选择git方式和HTTPS方式，而git方式通过配置ssh可以更加方便的使用，所以接下来配置ssh

### 3.1 生成SSH key
`ssh-keygen -t rsa -C "你的邮箱"`   
- `-t`是指定要创建的密钥类型，可以使用："rsa1"(SSH-1) "rsa"(SSH-2) "dsa"(SSH-2)。
- `-C`后面加上的是注释
- 生成密钥是第一个输入项是密钥的名字，第二个输入项`empty for no passphrase`留空并确认可达到登录时无需输入密码的效果

`ssh-add ~/.ssh/id_rsa`然后添加新生成的私钥，如果提示`Could not open a connection to your authentication agent.`,那么执行以下命令，然后再执行一次：
```git
eval `ssh-agent -s`
```

### 3.2 用config管理多个密钥
在`.ssh`目录下新建config文件，写入
```
#github【名称】
Host github.com【主机地址】
HostName github.com【主机名】
User git【用户名】
IdentityFile ~/.ssh/github【私钥路径】
```
以上内容为一组，写入保存后即可用 `ssh github.com` 来进行连接测试。

### 3.3 SSH连接测试
```
ssh -T git@"git服务器地址"
```

**注：**
- 创建成功后务必进行一次ssh测试
- Windows下，ssh密钥的目录在~/.ssh,生成文件中带`.pub`后缀的是公钥，add到Gitlab或GitHub的SSH配置中

## 4. 新建或者克隆项目
### 4.1 新建
git init

### 4.2 克隆
git clone 地址

## 5. 至此，愉快的开始吧~


# 四、常见问题

## 1. Git忽略规则及.gitignore规则不生效的解决办法
有时候添加顾略规则会发现并不生效，原因是.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：
```git
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```
解决方法来自：http://www.pfeng.org/archives/840

## 2. 密码不小心提交到了远程分支
当密码提交到远程分支以后，如果后来把文件加入忽略列表，但是以前的commit中还会留有密码文件。这个时候就得用到核弹级命令`git filter-branch`，之所以叫核弹级，是因为这个命令十分危险，理论上是可以对你的分支做任何操作。
使用方法：
1. 首先关闭远程分支的分支保护；
2. 然后执行
```git
git filter-branch --index-filter 'git rm --cached --ignore-unmatch <filename>' --tag-name-filter cat -- --all
```
3. 接着
```git
git push origin master --force
```
4. 最后别忘了把远程分支又改回保护状态。

## 3. 修改commit的信息
- 如果没有进行新的提交`git commit --amend`
- 如果进行了新的提交`git reset --soft xxx`，xxx是提交ID，但是会把后面的提交都变成一次，因此最好不要这么使用。

# 五、总结
至此git整理完毕，主要取材取[git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)，主要用于方便自己查阅。
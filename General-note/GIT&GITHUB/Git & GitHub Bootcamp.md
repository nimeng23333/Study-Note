---
title: Git & GitHub Bootcamp
---

Push是将本地放到远程仓库
Pull根据配置可以是fetch + merge或fetch + rebase
Fetch是将远程仓库放到本地缓存区
amend commit增补提交

git本质是一个版本管理系统 VC工具
在windows上用Git Bash
git用户名及邮箱配置

```
git config user.name
git config --global user.name "xxx"

git config user.email
git config --global user.email xxx@xx.com
```

ls list的缩写 可以列出文件夹内的内容
~符号是指`C:\Users\Administrator`文件夹
```
start .            可以打开~文件夹
open .             是mac电脑打开文件夹的方式
ls 文件夹名字       可展示文件夹里的内容
ls -a              可以显示隐藏的文件
隐藏文件开头有.

pwd                      可以列出现在的地址
cd change directory      改现在的地址
cd d：
cd ~
cd ..                    回到上一级
```

![[../note_images/Pasted image 20230328165011.png]]

运用git创建新文件：touch
```
touch xxx.jpg xxx.后缀 可以创建批量的新文件
touch xxx/yyy/zzz.png 可以在指定地址创建新文件
```

创建新文件夹：mkdir
```
mkdir 新文件夹
mkdir 新文件夹1 新文件夹2 新文件夹3 可批量创建新文件夹
```

删除文件且是永久删除：rm xxx.后缀
删除文件夹且是永久删除：rm -rf xxx

```
git status 返回git文件夹的状态
git init 将某个文件夹初始为git仓库
```

一个文件夹初始化为git仓库以后，会有一个.git的隐藏文件来记录
如果一个文件夹被重复初始化将会出错

# Git Commit

git commit 可以理解为检查点，自己设定修改内容

仓库里的文件有修改的时候 git status会显示有内容修改但无追踪
修改了多个不同的文件，但其中几个存为一个commit，另外的存为一个commit的时候用git add 做多次commit

```
git add file1 file2           将文件放进缓存区
git add .                     将所有修改了的文件都放进缓存区
git commit                    直接提交会打开一个默认编辑器并存信息
最好用git commit -m "信息"
```

git log 可以看提交日志
q可以退出日志模式

一次commit只关注一个事情

git log的时候
```
git log --abbrev-commit 显示commit的短哈希值
git log --oneline 每个commit都只显示一行
```

amend commit 补充提交 只能补充最新提交的commit
```
git commit --amend
```

# .gitignore


不想或者不需要被git追踪的东西可以用.gitignore文件
```
在.gitignore文件里
.文件名    可以省略文件
文件夹名/  可以省略文件夹及里面的东西
*.log     可以省略该后缀的所有文件
```
[gitignore.io - 为你的项目创建必要的 .gitignore 文件 (toptal.com)](https://www.toptal.com/developers/gitignore/)
可以找到常用的ignore内容

HEAD -> 分支名 当前指向的branch

```
git branch            查看所有的分支
git branch 分支名      可以在现在的分支上创建一个新的分支
git switch 分支名      切换分支
git checkout 分支名    也可以切换分支
git switch -c 分支名   创建新的分支并切换
git checkout -b 分支名 创建新的分支并切换
```

在一个分支工作在其他分支也有，且没有commit的时候切换分支将会失去未提交的工作内容。
如果当前工作在其他分支不冲突的时候，未commit就提交的时候这个文件会跟着切换

```
git branch -d 分支名    可以删除分支，但当分支没有被merge的时候不允许删除
git branch -D 分支名    强制删除分支
git branch -m 分支名    给所在的分支改名
```

# Git Branch Merge

```
git switch 分支名1
git merge  分支名2
切换到分支1后，将分支2的内容merge进1
```

当merge有冲突的时候会在文件里高亮有冲突的地方，打开显示为

```
<<<<<<<<<<HEAD
原branch的信息
=============
要合并进来branch的信息
>>>>>>>>
```

将提示标志删除，并决定如何修改，保存，就可以git add及git commit了

![[../note_images/Pasted image 20230331173901.png]]

# Git Diff

```
git diff 会比较暂存区域区域与本地区域文件的区别
```
![[../note_images/Pasted image 20230331175956.png]]

```
diff --git a/文件（更早版本） b/文件（更新版本）
index 哈希值不用管
--- a/文件 用减号标注的都是a的内容
+++ b/文件 用加号标注的都是b的内容
@@ -3,4 +3,5 @@ 表示以下内容在a文件的第三行开始涉及4行，在b文件的3行开始，涉及五行
```

```
git diff HEAD       不管暂存与否都可以与仓库文件比较
而add以后commit之前用git diff不能显示区别，因为git diff是比较本地文件与暂存区
git diff --staged    可以比较暂存区与仓库文件
git diff --cached    与staged一样

git diff HEAD 文件名 可以只比较给定文件的新旧版本
git diff 文件名

git diff 分支1..分支2
git diff 分支1 分支2    可以比较两个分支之间的区别

git diff commit1..commit2 复制两个commit的哈希短值即可比较两个commit的区别
```

# Git Stash

当在某一个分支里工作但还不想提交需要到其他分支的时候可以用stash隐藏当前的未提交修改

```
git stash
git stash save
都可以隐藏暂存与未暂存的文件修改

git stash pop                  将隐藏的文件拿出来
git stash apply                将stash里的隐藏的文件应用，但不删掉stash里的文件，可以在多个分支一起应用这个修改
git stash list                 可以看到stash区的储存列表
git stash apply stash@{2}      应用某一条stash存的修改
git stash drop stash@{2}       删掉某一条stash存的修改
git stash clear                删掉全部stash存的修改
```

# Git Checkout

```
git checkout 哈希值                   可以回到之前的某次提交，处于一个detached head的状态
git switch 分支名                     切换到现在的某个分支
git checkout -                       回到现在的分支
git checkout HEAD~1                  回到上一个提交
git checkout HEAD~2                  回到上两个提交
git checkout HEAD 文件名              让文件回到最近提交的内容
git checkout -- 文件名                让文件回到最近提交的内容
```

# Git Restore

```
git restore 文件名                    让文件回到最近提交的内容
git restore --source HEAD~1 文件名    让文件回到上一个提交的内容
git restore --staged 文件名           让暂存的文件回到工作区
```

# Git Reset

```
git reset 哈希值            恢复到某一次提交（后面的提交全没有了，但内容会存在在工作区
git reset --hard 哈希值     恢复到某一次提交，后面的提交消失，且内容不存在工作区
git reset --hard HEAD~1
```

# Git Revert
```
git revert 哈希值          不会删掉前面的提交，将某次及之后提交的内容撤掉形成一个新的提交，会有冲突并确认
```
Push是将本地放到远程仓库
Pull根据配置可以是fetch + merge或fetch + rebase
Fetch是将远程仓库放到本地缓存区
amend commit增补提交

git本质是一个版本管理系统 
在windows上Git Bash
git用户名及邮箱配置
```
git config user.name
git config --global user.name "xxx"

git config user.email
git config --global user.email xxx@xx.com
```

ls list的缩写 可以列出文件夹内的内容
~符号是在`C:\Users\Administrator`文件夹
```
start . 可以打开~文件夹
mac电脑是 open .
ls 文件夹名字 可展示文件夹里的内容
ls -a 可以显示隐藏的文件
隐藏文件开头有.
```
![[Pasted image 20230328164415.png]]
![[Pasted image 20230328164439.png]]

```
pwd 可以列出现在的地址
cd change directory 改现在的地址
cd d：
cd ~
cd ..回到上一级
```
![[Pasted image 20230328165011.png]]

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

git commit 可以理解为检查点，自己设定修改内容

仓库里的文件有修改的时候 git status会显示有内容修改但无追踪
修改了多个不同的文件，但其中几个存为一个commit，另外的存为一个commit的时候用git add 做多次commit
git add file1 file2 将文件放进缓存区
git add . 将所有修改了的文件都放进缓存区
git commit直接提交会打开一个默认编辑器并存信息
最好用git commit -m ”信息“

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

不想或者不需要被git追踪的东西可以用.gitignore文件
```
在.gitignore文件里
.文件名 可以省略文件
文件夹名/ 可以省略文件夹及里面的东西
*.log 可以省略该后缀的所有文件
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
```
在一个分支工作在其他分支也有，且没有commit的时候切换分支将会失去未提交的工作内容
如果当前工作在其他分支不冲突的时候，未commit就提交的时候这个文件会跟着切换

```
git branch -d 分支名  可以删除分支，但当分支没有被merge的时候不允许删除
git branch -D 分支名 强制删除分支
git branch -m 分支名 给所在的分支改名
```

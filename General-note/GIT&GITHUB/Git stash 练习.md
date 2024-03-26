---
title: Git stash 练习
---
# Stashing Exercise

1.  Initialize a new git repo in a folder
    
2.  Create a file called `diary.txt`. Inside the file, add the following:
    
    ```
    I love my boss
    ```
    
3.  Add and commit the changes on the `master` branch
    
4.  Create a new branch called `the-truth`. Switch to it.
    
5.  In the `diary.txt` file, erase the contents and instead replace it with:
    
    ```
    I HATE MY BOSS
    I HATE MY BOSS
    I HATE MY BOSS
    I HATE MY BOSS
    I HATE MY BOSS
    ```
    
6.  Save the file
    
7.  **OH NO!** Your boss is walking towards you! Quick, switch over to the `master` branch!
    
8.  **WHATTT?** The `diary.txt` file still contains our confession? **Quick, stash the changes before your boss sees!!**
    
9.  Your `diary.txt` file should now only contain "I love my boss"
    
10.  As your boss walks by, add more lies to the `diary.txt` file:
    
```

I love my boss
I love my boss
I love my boss

```
    
11.  Add and commit your changes on the `master` branch.
    
12.  Now that your boss has left, it's safe to get back to the truth! Switch over to the `the-truth` branch.
    
13.  Retrieve the earlier changes that you stashed (I HATE MY BOSS x 5)
    
14.  Add and commit the changes on the `the-truth` branch

## 作业

```
Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ git init
Initialized empty Git repository in E:/Udemy/Git&Github Bootcamp/Exercise/.git/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise (master)
$ touch diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise (master)
$

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise (master)
$ git status
fatal: not a git repository (or any of the parent directories): .git

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ cd

Administrator@REBECCA-DESKTOP2023 MINGW64 ~
$ cd .

Administrator@REBECCA-DESKTOP2023 MINGW64 ~
$ cd e:

Administrator@REBECCA-DESKTOP2023 MINGW64 /e
$ ls
'$RECYCLE.BIN'/               'Web Development'/
 360Downloads/                 github/
 NAU课程/                      note/
 OBS录屏/                      passport.jpg
'Office 2019.zip'              微信文件/
 QQMusicCache/                 显卡驱动+系统质检-剪切到最后一个盘/
 QQ文件/                       电子书/
 SteamLibrary/                 视频/
'System Volume Information'/   计算机学习资料/
 Udemy/                        运营/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e
$ cd Udemy/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy
$ cd e
bash: cd: e: No such file or directory

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy
$ cd Git\&Github\ Bootcamp/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp
$ cd e
bash: cd: e: No such file or directory

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp
$ cd Exercise/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ cd stage-exercise/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stage-exercise
$ cd .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stage-exercise
$ cd ..

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ cd stash-exercise/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise
$ git init
Initialized empty Git repository in E:/Udemy/Git&Github Bootcamp/Exercise/stash-exercise/.git/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ touch diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        diary.txt

nothing added to commit but untracked files present (use "git add" to track)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git commit -m"111"
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        diary.txt

nothing added to commit but untracked files present (use "git add" to track)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        diary.txt

nothing added to commit but untracked files present (use "git add" to track)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git commit -m"111"
[master (root-commit) 380e2f6] 111
 1 file changed, 1 insertion(+)
 create mode 100644 diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git status
On branch master
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git switch -c the-truth
Switched to a new branch 'the-truth'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git status
On branch the-truth
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   diary.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash
Saved working directory and index state WIP on the-truth: 380e2f6 111

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git status
On branch the-truth
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git switch master
Switched to branch 'master'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git commit -m"111111"
[master d464952] 111111
 1 file changed, 1 insertion(+)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git status
On branch master
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git switch the-truth
Switched to branch 'the-truth'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git status
On branch the-truth
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash list
stash@{0}: WIP on the-truth: 380e2f6 111

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash pop
On branch the-truth
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   diary.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (e247236aedc948a3251c16da7c8087e62cf66a7a)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash list

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git add.
git: 'add.' is not a git command. See 'git --help'.

The most similar command is
        add

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git commit -m"222"
[the-truth 5cfe5cd] 222
 1 file changed, 1 insertion(+), 1 deletion(-)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git status
On branch the-truth
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash list

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash
Saved working directory and index state WIP on the-truth: 5cfe5cd 222

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash list
stash@{0}: WIP on the-truth: 5cfe5cd 222

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash apply
On branch the-truth
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   diary.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git commit -m"333"
[the-truth f7ae85e] 333
 1 file changed, 1 insertion(+), 1 deletion(-)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git stash list
stash@{0}: WIP on the-truth: 5cfe5cd 222

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (the-truth)
$ git switch master
Switched to branch 'master'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git stash apply
Auto-merging diary.txt
CONFLICT (content): Merge conflict in diary.txt
On branch master
Unmerged paths:
  (use "git restore --staged <file>..." to unstage)
  (use "git add <file>..." to mark resolution)
        both modified:   diary.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git commit -m"333"
[master a673bd2] 333
 1 file changed, 5 insertions(+), 1 deletion(-)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git stash list
stash@{0}: WIP on the-truth: 5cfe5cd 222

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git stash drop
Dropped refs/stash@{0} (6922abed621fe803631986ab2f0feafa333d9665)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git stash list

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ start diary.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git status
On branch master
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   diary.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$ git commit -m"333"
[master c4df74c] 333
 1 file changed, 1 insertion(+), 6 deletions(-)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/stash-exercise (master)
$
```
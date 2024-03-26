---
title: Git 练习
---

![[../note_images/Pasted image 20230328225610.png]]
```
Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Study-Note (main)
$ cd ..

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github
$ mrdir Shopping
bash: mrdir: command not found

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github
$ mkdir Shopping

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github
$ ls
Shopping/  Study-Note/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github
$ cd Shopping

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping
$ git init
Initialized empty Git repository in E:/github/Shopping/.git/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ ls -a
./  ../  .git/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ touch yard.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ ls
yard.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ touch groceries.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ ls
groceries.txt  yard.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        groceries.txt
        yard.txt

nothing added to commit but untracked files present (use "git add" to track)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git add
Nothing specified, nothing added.
hint: Maybe you wanted to say 'git add .'?
hint: Turn this message off by running
hint: "git config advice.addEmptyPathspec false"

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   groceries.txt
        new file:   yard.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git commit -m "create yard and groceries lists"
[master (root-commit) 2d25c2f] create yard and groceries lists
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 groceries.txt
 create mode 100644 yard.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master
nothing to commit, working tree clean

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   groceries.txt
        modified:   yard.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git add groceries.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   groceries.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   yard.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git commit -m "add ingredients for tomato soup"
[master 1b5e7da] add ingredients for tomato soup
 1 file changed, 3 insertions(+)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   yard.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git add yard.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   yard.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git commit -m "add items needed for garden box"
[master b19202d] add items needed for garden box
 1 file changed, 4 insertions(+)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   groceries.txt
        modified:   yard.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   groceries.txt
        modified:   yard.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git commit -m "add items needed to grow potatoes"
[master e94230f] add items needed to grow potatoes
 2 files changed, 4 insertions(+), 5 deletions(-)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/github/Shopping (master)
$ git log
commit e94230f3ef3362231b5b87c1b22154484a3825f2 (HEAD -> master)
Author: Yutong Dai <91735864+nimeng23333@users.noreply.github.com>
Date:   Tue Mar 28 22:54:13 2023 +0800

    add items needed to grow potatoes

commit b19202d4e4e722df4001a4a8e32e49dbab152599
Author: Yutong Dai <91735864+nimeng23333@users.noreply.github.com>
Date:   Tue Mar 28 22:53:08 2023 +0800

    add items needed for garden box

commit 1b5e7da70607e3025af94c88dc7267e54aeb040f
Author: Yutong Dai <91735864+nimeng23333@users.noreply.github.com>
Date:   Tue Mar 28 22:51:59 2023 +0800

    add ingredients for tomato soup

commit 2d25c2f5518ff01ec0022d3c8ac2cc3da4f6bec5
Author: Yutong Dai <91735864+nimeng23333@users.noreply.github.com>
Date:   Tue Mar 28 22:50:33 2023 +0800

    create yard and groceries lists

```
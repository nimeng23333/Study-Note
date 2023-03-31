# Branching Exercise

题目

1.  Make a new folder called `Patronus`
    
2.  Make a new git repo inside the folder (make sure you're not in an existing repo)
    
3.  Create a new file called `patronus.txt` (leave it empty for now)
    
4.  Add and commit the empty file, with the message "add empty patronus file"
    
5.  Immediately make a new branch called `harry` and another branch called `snape` (both based on the master branch)
    
6.  Move to the `harry` branch using the "new" command to change branches.
    
7.  In the `patronus.txt` file, add the following:
    
    ```
    HARRY'S PATRONUS
    
       /|       |\\
    `__\\\\       //__'
       ||      ||
     \\__`\\     |'__/
       `_\\\\   //_'
       _.,:---;,._
       \\_:     :_/
         |@. .@|
         |     |
         ,\\.-./ \\
         ;;`-'   `---__________-----.-.
         ;;;                         \\_\\
         ';;;                         |
          ;    |                      ;
           \\   \\     \\        |      /
            \\_, \\    /        \\     |\\
              |';|  |,,,,,,,,/ \\    \\ \\_
              |  |  |           \\   /   |
              \\  \\  |           |  / \\  |
               | || |           | |   | |
               | || |           | |   | |
               | || |           | |   | |
               |_||_|           |_|   |_|
              /_//_/           /_/   /_/
    ```
    
8.  Add and commit the changes, with the commit message "add harry's stag patronus"
    
9.  Move over to the `snape` branch using the "older" command to change branches.
    
10.  Put the following text in the `patronus.txt` file:
    

````
```
SNAPE'S PATRONUS
                   .     _,
                   |`\\__/ /
                   \\  . .(
                    | __T|
                   /   |
      _.---======='    |
     //               {}
    `|      ,   ,     {}
     \\      /___;    ,'
      ) ,-;`    `\\  //
     | / (        ;||
     ||`\\\\        |||
     ||  \\\\       |||
     )\\   )\\      )||
     `"   `"      `""
```
````

11.  Add and commit the changes on the `snape` branch with the commit message "add snape's doe patronus"
12.  Next, create a new branch based upon the `snape` branch called `lily`
13.  Move to the `lily` branch
14.  Edit the `patronus.txt` file so that it says `LILY'S PATRONUS` at the top instead of `SNAPE'S PATRONUS` (leave the doe ascii-art alone)
15.  Add and commit the change with the message "add lily's doe patronus"
16.  Run a git command to list all branches (you should see 4)
17.  **Bonus:** delete the `snape` branch (poor Snape)


操作：

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ mkdir Patronus

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ ls
Patronus/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ cd Patronus/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus
$ git inite
git: 'inite' is not a git command. See 'git --help'.

The most similar command is
        init

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus
$ git init
Initialized empty Git repository in E:/Udemy/Git&Github Bootcamp/Exercise/Patronus/.git/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ touch patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ ls
patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        patronus.txt

nothing added to commit but untracked files present (use "git add" to track)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git add patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   patronus.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git commit -m "add empty patronus file"
[master (root-commit) 73ac2c4] add empty patronus file
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git switch -c harry
Switched to a new branch 'harry'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git switch master
Switched to branch 'master'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git switch snape
fatal: invalid reference: snape

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git branch
  harry
* master

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git branch snape

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git branch
  harry
* master
  snape

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git switch harry
Switched to branch 'harry'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ cd patrounus.txt
bash: cd: patrounus.txt: No such file or directory

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ start patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git sta
git: 'sta' is not a git command. See 'git --help'.

The most similar commands are
        status
        stage
        stash

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git status
On branch harry
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   patronus.txt

no changes added to commit (use "git add" and/or "git commit -a")

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git add
Nothing specified, nothing added.
hint: Maybe you wanted to say 'git add .'?
hint: Turn this message off by running
hint: "git config advice.addEmptyPathspec false"

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git status
On branch harry
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   patronus.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git commit -m "add harry's stag patronus"
[harry c701031] add harry's stag patronus
 1 file changed, 26 insertions(+)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git checkout snape
Switched to branch 'snape'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (snape)
$ start patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (snape)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (snape)
$ git commit -m "add snape's doe patronus"
[snape 6d6f676] add snape's doe patronus
 1 file changed, 16 insertions(+)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (snape)
$ git branch lily

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (snape)
$ git switch lily
Switched to branch 'lily'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (lily)
$ start patronus.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (lily)
$ git add .

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (lily)
$ git commit -m "add lily's doe patronus"
[lily 4085a05] add lily's doe patronus
 1 file changed, 1 insertion(+), 1 deletion(-)

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (lily)
$ git branch
  harry
* lily
  master
  snape

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (lily)
$ git switch snape
Switched to branch 'snape'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (snape)
$ git switch lily
Switched to branch 'lily'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (lily)
$ git switch harry
Switched to branch 'harry'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (harry)
$ git switch master
Switched to branch 'master'

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git -d snape
unknown option: -d
usage: git [-v | --version] [-h | --help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           [--config-env=<name>=<envvar>] <command> [<args>]

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git branch -d snape
error: The branch 'snape' is not fully merged.
If you are sure you want to delete it, run 'git branch -D snape'.

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git branch -D snape
Deleted branch snape (was 6d6f676).

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/Patronus (master)
$ git branch
  harry
  lily
* master

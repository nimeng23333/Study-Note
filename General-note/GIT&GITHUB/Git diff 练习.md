# Git Diff Exercise

This exercise comes with a starter repo that you will need to clone down to your machine. We will be working with two bands: Queen and Fleetwood Mac and their various lineups over the years.

## Part 1: Setup

Please run the following commands from your terminal. Make sure you are not inside of a Git repo.

```
git clone <https://github.com/Colt/git-diff-exercise>
```

-   Change into the newly created folder called `git-diff-exercise`. You will be on a branch called `current` by default.
-   Run `git switch 1970s`
-   You should now have two branches that you can move between: `current` and `1970s` Each branch contains two files: `queen.txt` and `fleetwoodmac.txt`

## Part 2: The Diffs

1.  Compare the difference between the `1970s` branch and the `current` branch across all files
2.  Compare the difference between the `1970s` branch and the `current` branch but only for the queen.txt file
3.  Switch over to the `current` branch if you are not currently on it. Run a diff to compare the current HEAD to the previous commit (you could use the commit hash or reference HEAD's parent commit)
4.  While on the `current` branch, change the `queen.txt` file by replacing Adam Lambert with your own name. Save the change. Add `queen.txt` to the staging area. DO NOT COMMIT YET!
5.  Edit the `fleetwoodmac.txt` file, changing the lead singer from Stevie Nicks to Stevie Chicks (one of my chickens). Save the file, but do not stage it.
6.  Run a diff that would reveal the unstaged changes in the working directory (you should see only the change to `fleetwoodmac.txt` printed out)
7.  Run a diff that would reveal only the staged changes (you should only see the change to `queen.txt` printed out)
8.  Run a diff that prints all changes (staged and unstaged) since the prior commit (you should see both changes printed out)


# 操作

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise
$ cd git-diff-exercise/

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git switch 1970s
Switched to a new branch '1970s'
branch '1970s' set up to track 'origin/1970s'.

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (1970s)
$ ls
fleetwoodmac.txt  queen.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (1970s)
$ git branch
* 1970s
  current

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (1970s)
$ git diff 1970s current
diff --git a/fleetwoodmac.txt b/fleetwoodmac.txt
index 6f44367..653b184 100644
--- a/fleetwoodmac.txt
+++ b/fleetwoodmac.txt
@@ -1,5 +1,5 @@
-Lead Vocals: Jeremy Spencer
-Lead Guitar: Lindsey Buckingham
+Lead Vocals: Stevie Nicks
+Lead Guitar: Mike Campbell
 Bass: John McVie
 Drums: Mick Fleetwood
-Keyboard: None
\ No newline at end of file
+Keyboard: Christine McVie
\ No newline at end of file
diff --git a/queen.txt b/queen.txt
index 2ab2d04..0e0fa0d 100644
--- a/queen.txt
+++ b/queen.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Freddie Mercury
+Lead Vocals: Adam Lambert
 Lead Guitar: Brian May
-Bass: Mike Grose
+Bass: Neil Fairclough
 Drums: Roger Taylor
\ No newline at end of file

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (1970s)
$ git diff 1970s current queen.txt
diff --git a/queen.txt b/queen.txt
index 2ab2d04..0e0fa0d 100644
--- a/queen.txt
+++ b/queen.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Freddie Mercury
+Lead Vocals: Adam Lambert
 Lead Guitar: Brian May
-Bass: Mike Grose
+Bass: Neil Fairclough
 Drums: Roger Taylor
\ No newline at end of file

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (1970s)
$ git switch 1970s
Already on '1970s'
Your branch is up to date with 'origin/1970s'.

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (1970s)
$ git switch current
Switched to branch 'current'
Your branch is up to date with 'origin/current'.

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git diff 09cbcc9df5625dabf78ecbc39f29b6984e7d70e1..15db96011ee62521736308b2ba5942a14486a347
diff --git a/queen.txt b/queen.txt
index 0e0fa0d..f7f4a33 100644
--- a/queen.txt
+++ b/queen.txt
@@ -1,4 +1,4 @@
 Lead Vocals: Adam Lambert
 Lead Guitar: Brian May
-Bass: Neil Fairclough
+Bass: John Deacon
 Drums: Roger Taylor
\ No newline at end of file

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ ls
fleetwoodmac.txt  queen.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ start fleetwoodmac.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ start queen.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git add queen.txt

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git status
On branch current
Your branch is up to date with 'origin/current'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   queen.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   fleetwoodmac.txt


Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git diff HEAD fleetwoodmac.txt
diff --git a/fleetwoodmac.txt b/fleetwoodmac.txt
index 653b184..8c384c0 100644
--- a/fleetwoodmac.txt
+++ b/fleetwoodmac.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Stevie Nicks
+Lead Vocals: Stevie Chicks
 Lead Guitar: Mike Campbell
 Bass: John McVie
 Drums: Mick Fleetwood

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git diff --staged
diff --git a/queen.txt b/queen.txt
index 0e0fa0d..7e183d5 100644
--- a/queen.txt
+++ b/queen.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Adam Lambert
+Lead Vocals: me
 Lead Guitar: Brian May
 Bass: Neil Fairclough
 Drums: Roger Taylor
\ No newline at end of file

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git diff --cached
diff --git a/queen.txt b/queen.txt
index 0e0fa0d..7e183d5 100644
--- a/queen.txt
+++ b/queen.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Adam Lambert
+Lead Vocals: me
 Lead Guitar: Brian May
 Bass: Neil Fairclough
 Drums: Roger Taylor
\ No newline at end of file

Administrator@REBECCA-DESKTOP2023 MINGW64 /e/Udemy/Git&Github Bootcamp/Exercise/git-diff-exercise (current)
$ git diff HEAD
diff --git a/fleetwoodmac.txt b/fleetwoodmac.txt
index 653b184..8c384c0 100644
--- a/fleetwoodmac.txt
+++ b/fleetwoodmac.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Stevie Nicks
+Lead Vocals: Stevie Chicks
 Lead Guitar: Mike Campbell
 Bass: John McVie
 Drums: Mick Fleetwood
diff --git a/queen.txt b/queen.txt
index 0e0fa0d..7e183d5 100644
--- a/queen.txt
+++ b/queen.txt
@@ -1,4 +1,4 @@
-Lead Vocals: Adam Lambert
+Lead Vocals: me
 Lead Guitar: Brian May
 Bass: Neil Fairclough
 Drums: Roger Taylor
\ No newline at end of file

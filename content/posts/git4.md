---
title: "Git IV: Remote Repository 2"
date: 2023-08-23T14:04:23+02:00
draft: true
toc: false
images:
tags:
  - git
---
---
<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Three-Way Merges](#three-way-merges)
   * [Why Are Three-Way Merges Important?](#why-are-three-way-merges-important)
   * [Scenario](#scenario)
      + [Defining Upstream Branches](#defining-upstream-branches)
   * [Three-Way Merge in Practice](#three-way-merge-in-practice)
   * [Pulling Changes from a Remote Repository](#pulling-changes-from-a-remote-repository)
- [Merge Conflicts](#merge-conflicts)
   * [Introducing](#introducing)
   * [How to Resolve Merge Conflicts](#how-to-resolve-merge-conflicts)
   * [Setting Up a Merge Conflict Scenario](#setting-up-a-merge-conflict-scenario)
   * [The Merge Conflict Resolution Process](#the-merge-conflict-resolution-process)
   * [Syncing the Repositories](#syncing-the-repositories)

<!-- TOC end -->
---

<!-- TOC --><a name="three-way-merges"></a>
## Three-Way Merges
<!-- TOC --><a name="why-are-three-way-merges-important"></a>
### Why Are Three-Way Merges Important?
Three-way merges are a bit more complicated than fast-forward merges because they create merge commits and they may lead to merge conflicts.

_Merge conflicts_ arise when:
- you **merge** two branches where **different changes** have been made to the **same parts** in the **same file**(s)
- if in one branch a **file was deleted** that **was edited in the other branch**.

Three-way merges occur when the **development histories of the branches** involved in the merge **have diverged**.

<!-- TOC --><a name="scenario"></a>
### Scenario

```shell
myProject$ echo "Brown is not a color" >> othercolors

myProject$ git add othercolors 

myProject$ git commit -m "brown"
[main f47f818] brown
 1 file changed, 1 insertion(+)
 create mode 100644 othercolors

myProject$ git log 
commit f47f818881e30a4517f178a6730949ad16158df5 (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 23:25:43 2023 +0100

    brown

commit b177438a693f8c76426bb7198e46f270441e811b (origin/main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 20:58:48 2023 +0100

    green


```
![](merge2.png)

<!-- TOC --><a name="defining-upstream-branches"></a>
#### Defining Upstream Branches
>`git branch -u <shortname>/<branch_name>` Define an upstream branch for the current local branch

```shell
myProject$ git branch -vv # To check whether an upstream branch has been defined
* main f47f818 brown

myProject$ git branch -u origin/main # explicitly states that an upstream branch has been set
branch 'main' set up to track 'origin/main'.

myProject$ git branch -vv
* main f47f818 [origin/main: ahead 1] brown
```
Now that we have defined an upstream branch for your local main branch, we can use the `git push` command without any arguments.

```shell
myProject$ git push 
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 12 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 285 bytes | 285.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:YasszxD/myProject-remote.git
   b177438..f47f818  main -> main
   
myProject$ git log 
commit f47f818881e30a4517f178a6730949ad16158df5 (HEAD -> main, origin/main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 23:25:43 2023 +0100

    brown
```
-> We pushed your local main branch to the remote repository.
![](merge1.png)
![](merge3.png)

**⛔ Now, our friend will continue working on the local main branch in their local repository _without_ fetching the changes you pushed to the remote main branch.**

```shell
friendProject$ git status 
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean

friendProject$ echo "Blue is the fifth color" >> colors 

friendProject$ git add colors 

friendProject$ git commit -m "blue"
[main 052e915] blue
 1 file changed, 1 insertion(+)

friendProject$ git push
To github.com:YasszxD/myProject-remote.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'github.com:YasszxD/myProject-remote.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

friendProject$ git log
commit 052e915d7337e2a2c6a3995abe88ed9549cb09a4 (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:03:10 2023 +0100

    blue

commit b177438a693f8c76426bb7198e46f270441e811b (origin/main, origin/HEAD)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 20:58:48 2023 +0100

    green


```
-> The output of the git push command shows that your friend gets an error. They are not able to push their changes to the remote repository.
-> The development histories of the local main branch and the remote main branch have diverged, and Git is not able to merge changes from one into the other with a simple fast-forward merge.

![](merge4.png)
-> The local main branch points to the blue commit.
-> In the rainbow-remote repository, the main branch points to the brown commit.
<!-- TOC --><a name="three-way-merge-in-practice"></a>
### Three-Way Merge in Practice
Having read the error message Git presented, your friend is now going to fetch the changes from the remote main branch into their local repository. This will update the origin/main remote-tracking branch. Then they will merge the origin/main remote-tracking branch into their local main branch.
```shell
friendProject$ git fetch
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 265 bytes | 265.00 KiB/s, done.
From github.com:YasszxD/myProject-remote
   b177438..f47f818  main       -> origin/main

friendProject$ git merge origin/main
Merge made by the 'ort' strategy.
 othercolors | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 othercolors
 ```
This will open in your text editor asking for message:
```shell
Merge remote-tracking branch 'origin/main'
# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
```
 
```shell

friendProject$ git log
commit d230e4a54ae6c8e2d5643758af3695ad9760e546 (HEAD -> main)
Merge: 052e915 f47f818
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:09:51 2023 +0100

    Merge remote-tracking branch 'origin/main'
    blue three-way merge

commit 052e915d7337e2a2c6a3995abe88ed9549cb09a4
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:03:10 2023 +0100

    blue

commit f47f818881e30a4517f178a6730949ad16158df5 (origin/main, origin/HEAD)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 23:25:43 2023 +0100

    brown

```
![](merge5.png)

Our friend will push their changes to the remote repository to make sure it’s updated.
```shell
friendProject$ git push
Enumerating objects: 9, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 578 bytes | 578.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:YasszxD/myProject-remote.git
   f47f818..d230e4a  main -> main

friendProject$ git log 
commit d230e4a54ae6c8e2d5643758af3695ad9760e546 (HEAD -> main, origin/main, origin/HEAD)
Merge: 052e915 f47f818
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:09:51 2023 +0100

    Merge remote-tracking branch 'origin/main'
    blue three-way merge

commit 052e915d7337e2a2c6a3995abe88ed9549cb09a4
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:03:10 2023 +0100

    blue

commit f47f818881e30a4517f178a6730949ad16158df5
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 23:25:43 2023 +0100

    brown


```
![](merge6.png)
<!-- TOC --><a name="pulling-changes-from-a-remote-repository"></a>
### Pulling Changes from a Remote Repository
 the term _pull_ or _pulling_ to refer to the process of fetching data from a remote repository and integrating(merge or rebase) it into a branch in a local repository in one go.
 - If the development histories of the local branch and remote branch in a git pull have _not_ diverged, then by default a fast-forward merge will occur.
 - If the development histories of the local branch and the remote branch in a `git pull` _have_ diverged, then you must tell Git whether you want to integrate the changes by merging or rebasing (otherwise, you’ll get an error). To tell Git to integrate the changes by merging, you must pass in the `--no-rebase` option. To tell Git to integrate the changes by rebasing, you must pass in the `--rebase` option.
 
 >`git pull <shortname> <branch_name>` Fetch and integrate changes from the `<shortname>` remote repository for the specified `<branch_name>`
 >`git pull` If an upstream branch is defined for the current branch, fetch and integrate changes from the defined upstream branch
 
```shell
myProject$ git pull 
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 5 (delta 1), reused 5 (delta 1), pack-reused 0
Unpacking objects: 100% (5/5), 558 bytes | 558.00 KiB/s, done.
From github.com:YasszxD/myProject-remote
   f47f818..d230e4a  main       -> origin/main
Updating f47f818..d230e4a
Fast-forward
 colors | 1 +
 1 file changed, 1 insertion(+)

myProject$ git log 
commit d230e4a54ae6c8e2d5643758af3695ad9760e546 (HEAD -> main, origin/main)
Merge: 052e915 f47f818
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:09:51 2023 +0100

    Merge remote-tracking branch 'origin/main'
    blue three-way merge

commit 052e915d7337e2a2c6a3995abe88ed9549cb09a4
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:03:10 2023 +0100

    blue

commit f47f818881e30a4517f178a6730949ad16158df5
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 23:25:43 2023 +0100

    brown


```
![](merge7.png)


<!-- TOC --><a name="merge-conflicts"></a>
## Merge Conflicts
<!-- TOC --><a name="introducing"></a>
### Introducing
- Occurs when the same file has been edited in different ways in the two branches involved in the merge.
- Occurs when we integrate two branches where in one branch a specific file has been edited and in the other branch that same file has been deleted.
<!-- TOC --><a name="how-to-resolve-merge-conflicts"></a>
### How to Resolve Merge Conflicts
When merge conflicts happen, you will see a set of special markers in each of the files involved that indicate where the conflicts occur. These markers, called _conflict markers_.
```c
<<<<<<< HEAD
{Content of the target branch}
=======
{Content of the source branch}
>>>>>>> refs/remote/origin/main
```
There are two steps to resolving merge conflicts: 
1. Decide what to keep, edit the content, and remove the conflict markers.
2. Add the file(s) you have edited to the staging area and commit your changes.
<!-- TOC --><a name="setting-up-a-merge-conflict-scenario"></a>
### Setting Up a Merge Conflict Scenario
To set up a three-way merge with merge conflicts, we and our friend have to make different changes to the same part in the same file.
1. Add the color indigo in the `colors` file in our myProject repository and push the updated main branch to the remote repository.
```shell
myProject$ echo "Indigo is the sixth color" >> colors 

myProject$ git add colors 

myProject$ git commit -m "indigo"
[main 4599947] indigo
 1 file changed, 1 insertion(+)
yexz@debian:~/Desktop/myProject$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 316 bytes | 316.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:YasszxD/myProject-remote.git
   d230e4a..4599947  main -> main

myProject$ git log
commit 4599947ba168beb3f923b2165fb161ae20f347bb (HEAD -> main, origin/main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:31:45 2023 +0100

    indigo

commit d230e4a54ae6c8e2d5643758af3695ad9760e546
Merge: 052e915 f47f818
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:09:51 2023 +0100

    Merge remote-tracking branch 'origin/main'
    blue three-way merge



```
![](conflict1.png)
2. Our friend will add the color violet to the same line in the `colors` file and make a commit without first pulling the changes you made from the myProject-remote repository into their local main branch.
```shell
friendProject$ echo "Violet is the seventh color" >> colors 

friendProject$ git add colors 

friendProject$ git commit -m "violet"
[main eaa1d12] violet
 1 file changed, 1 insertion(+)
yexz@debian:~/Desktop/friendProject$ git log
commit eaa1d12dff781b0a0e814b6669170b03893e2b7a (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:43:44 2023 +0100

    violet

commit d230e4a54ae6c8e2d5643758af3695ad9760e546 (origin/main, origin/HEAD)
Merge: 052e915 f47f818
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 00:09:51 2023 +0100

    Merge remote-tracking branch 'origin/main'
    blue three-way merge


```
![](conflict2.png)
3. Now, Our friend will need to fetch your changes from the remote repository and integrate them before they can push their changes to the remote repository.
```shell
friendProject$ git fetch 
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), 296 bytes | 296.00 KiB/s, done.
From github.com:YasszxD/myProject-remote
   d230e4a..4599947  main       -> origin/main

friendProject$ git status 
On branch main
Your branch and 'origin/main' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
nothing to commit, working tree clean

friendProject$ git log --all
commit eaa1d12dff781b0a0e814b6669170b03893e2b7a (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:43:44 2023 +0100

    violet

commit 4599947ba168beb3f923b2165fb161ae20f347bb (origin/main, origin/HEAD)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:31:45 2023 +0100

    indigo


```
-> the `git status` command states that Your branch and 'origin/main' have diverged, and have 1 and 1 different commits each, respectively.
![](conflict3.png)
Now, friend is going to carry out a three-way merge to integrate the latest changes on the remote main branch into their local main branch so they can push the updated main branch to the remote repository.
<!-- TOC --><a name="the-merge-conflict-resolution-process"></a>
### The Merge Conflict Resolution Process
```shell
friendProject$ git merge origin/main
Auto-merging colors
CONFLICT (content): Merge conflict in colors
Automatic merge failed; fix conflicts and then commit the result.

friendProject$ git status
On branch main
Your branch and 'origin/main' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   colors

no changes added to commit (use "git add" and/or "git commit -a")

friendProject$ cat colors 
red is the first color
orange is the second color
Yellow is the third
Green is the fourth
Blue is the fifth color
<<<<<<< HEAD
Violet is the seventh color
=======
Indigo is the sixth color
>>>>>>> origin/main

friendProject$ vi colors #edit the colors file so we save both line in the correct order

friendProject$ cat colors 
red is the first color
orange is the second color
Yellow is the third
Green is the fourth
Blue is the fifth color
Indigo is the sixth color
Violet is the seventh color

friendProject$ git add colors 

friendProject$ git status 
On branch main
Your branch and 'origin/main' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:
	modified:   colors


friendProject$ git commit -m "merge commit 2"
[main b775a51] merge commit 2

friendProject$ git log
commit b775a51a56820bc3757c8e26aa71b5fcd6054597 (HEAD -> main)
Merge: eaa1d12 4599947
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 21:10:30 2023 +0100

    merge commit 2

commit eaa1d12dff781b0a0e814b6669170b03893e2b7a
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:43:44 2023 +0100

    violet

commit 4599947ba168beb3f923b2165fb161ae20f347bb (origin/main, origin/HEAD)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:31:45 2023 +0100

    indigo

commit d230e4a54ae6c8e2d5643758af3695ad9760e546
yexz@debian:~/Desktop/friendProject$ 

```

![](conflict4.png)
<!-- TOC --><a name="syncing-the-repositories"></a>
### Syncing the Repositories
```shell
friendProject$ git push
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 12 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 596 bytes | 596.00 KiB/s, done.
Total 6 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To github.com:YasszxD/myProject-remote.git
   4599947..b775a51  main -> main

friendProject$ git log 
commit b775a51a56820bc3757c8e26aa71b5fcd6054597 (HEAD -> main, origin/main, origin/HEAD)
Merge: eaa1d12 4599947
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 21:10:30 2023 +0100

    merge commit 2

commit eaa1d12dff781b0a0e814b6669170b03893e2b7a
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:43:44 2023 +0100

    violet
```

```shell
myProject$ git pull
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 2), reused 6 (delta 2), pack-reused 0
Unpacking objects: 100% (6/6), 576 bytes | 576.00 KiB/s, done.
From github.com:YasszxD/myProject-remote
   4599947..b775a51  main       -> origin/main
Updating 4599947..b775a51
Fast-forward
 colors | 1 +
 1 file changed, 1 insertion(+)

myProject$ git log
commit b775a51a56820bc3757c8e26aa71b5fcd6054597 (HEAD -> main, origin/main)
Merge: eaa1d12 4599947
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 21:10:30 2023 +0100

    merge commit 2

commit eaa1d12dff781b0a0e814b6669170b03893e2b7a
Author: Yexz <abdelhedi166@gmail.com>
Date:   Mon Sep 25 20:43:44 2023 +0100

    violet

```
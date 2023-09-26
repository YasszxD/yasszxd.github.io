---
title: "Git II: Local repositories"
date: 2023-08-23T14:04:23+02:00
draft: false
toc: false
images:
tags:
  - git
---
---
<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [_Repositories_](#_repositories_)
   * [Initializing a Local Repository](#initializing-a-local-repository)
   * [The Areas of Git](#the-areas-of-git)
   * [Adding a File to a Git Project](#adding-a-file-to-a-git-project)
- [Making a Commit](#making-a-commit)
   * [The Two Steps to Make a Commit](#the-two-steps-to-make-a-commit)
   * [making a commit](#making-a-commit)
- [Branches](#branches)
   * [What is a branch](#what-is-a-branch)
      + [branches as movable pointers to commits](#branches-as-movable-pointers-to-commits)
      + [Unmodified and Modified Files](#unmodified-and-modified-files)
   * [Creating a Branch](#creating-a-branch)
   * [what is `HEAD —> main`](#what-is-head-main)
   * [Switching Branches](#switching-branches)
   * [Working on a Separate Branch](#working-on-a-separate-branch)
   * [Git protects you from losing uncommitted changes](#git-protects-you-from-losing-uncommitted-changes)
   * [Switching branches changes files in the working directory](#switching-branches-changes-files-in-the-working-directory)
- [Merging](#merging)
   * [Types of Merges ](#types-of-merges)
      + [Fast-forward merges](#fast-forward-merges)
      + [three-way merge](#three-way-merge)
   * [Doing a Fast-Forward Merge](#doing-a-fast-forward-merge)
      + [EXECUTE A MERGE](#execute-a-merge)
   * [Checking Out Commits](#checking-out-commits)
   * [Creating a Branch and Switching onto It in One Go](#creating-a-branch-and-switching-onto-it-in-one-go)

<!-- TOC end -->
---


<!-- TOC --><a name="_repositories_"></a>
## _Repositories_
A _repository_ (also known as a _repo_) is how we refer to **a project version controlled by Git**. In reality, there are two types of repositories:
- A _local repository_ is a repository that is stored on a computer. 
- A _remote repository_ is a repository that is hosted on a hosting service. **(in the next part)**
<!-- TOC --><a name="initializing-a-local-repository"></a>
### Initializing a Local Repository
>A local repository is represented by **a hidden directory** called `.git` that exists **within a project directory**. It contains all the data on the changes that have been made to the files in a project.

```shell
git init -b <branch_name> # master is default
# git init -b main
```
<!-- TOC --><a name="the-areas-of-git"></a>
### The Areas of Git
- Working directory: The _working directory_ contains the files and directories in the project directory that represent one version of a project
- Staging area:  It is where you can add and remove files, when you are preparing **what you want to include** in the next saved version of your project (**your next commit**). The staging area is represented by a file in the `.git` directory called `index`.
- Commit history: The _commit history_ is where commits exists. It is represented by the `objects` directory inside the `.git` directory.  
> A _commit_ in Git is basically one version of a project, you can think of it as a **snapshot** of a project.
- Local repository
![Areas.png](/img/gitArticles/Areas.png)


<!-- TOC --><a name="adding-a-file-to-a-git-project"></a>
### Adding a File to a Git Project
there is two states for you files in the project directory:
- _untracked file_: a file **in the working directory** that Git is **not version controlling**. It has **never been added to the staging area** and it **has never been included in a commit**
- _tracked file_:  a file that is **version controlled**
> Every new file in a project version controlled by Git needs to be explicitly added to the staging area and then included in a commit in order to become a tracked file

<!-- TOC --><a name="making-a-commit"></a>
## Making a Commit
<!-- TOC --><a name="the-two-steps-to-make-a-commit"></a>
### The Two Steps to Make a Commit
1. Add all the files you want to include in the next commit to the staging area. 
2. Make a commit with a commit message.


<!-- TOC --><a name="making-a-commit"></a>
### making a commit
1. lets create a file and add some text to it
```shell
myProject$ echo "red is the first color" > colors
```
2. check the working directory status
>`git status` Show the state of the working directory and the staging area.
```shell
myProject$ git status
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	colors

nothing added to commit but untracked files present (use "git add" to track)
```
- `colors`  file is an _untracked file_ (it must be added to the staging area and included in a commit).
- there are No commits yet.

3. Add the file to the staging area.
>`git add <filename>` Add one file to the staging area
>`git add <filename> <filename> ...` Add multiple files to the staging area
>`git add -A` Add all the files in the working directory that have been edited or changed to the staging area

- before adding lets check our `local repository` files
```shell
myProject$ ls .git/
branches/    description  hooks/       objects/     
config       HEAD         info/        refs/
```
- adding the file to the `staging area` (_copies_ the file from the `working directory` into the `staging area`).
```shell
myProject$ git add colors
```
- lets check again our `local repository` files
```shell
myProject$ ls .git/
branches/    description  hooks/       info/        refs/        
config       HEAD         index        objects/
```
You can notice the newly created `index` file  which represent the staging area.
- lets check our `project directory` status
```shell
myProject$ git status 
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   colors
```
4. Making a commit
>`git commit -m <message>` Create a new commit with a commit message

```shell
myProject$ git commit -m "red"
[main (root-commit) 335bd5d] red
 1 file changed, 1 insertion(+)
 create mode 100644 colors
```
5. Viewing a List of Commits
>`git log` Show a list of commits in reverse chronological order

```shell
myProject$ git log
commit 335bd5d9d73162eb884f2867979550be978430b8 (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red

```

<!-- TOC --><a name="branches"></a>
## Branches
<!-- TOC --><a name="what-is-a-branch"></a>
### What is a branch
Branches in Git are movable pointers to commits.

```shell
myProject$ git log
commit 335bd5d9d73162eb884f2867979550be978430b8 (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red

```
-> The branch or branches that appear inside the parentheses in the git log output are the branches that point to that commit.
![redcommit](/img/gitArticles/redcommit.png)
<!-- TOC --><a name="branches-as-movable-pointers-to-commits"></a>
#### branches as movable pointers to commits
```shell
myProject$ cat .git/refs/heads/main 
335bd5d9d73162eb884f2867979550be978430b8
```
The term “refs” stands for “references.” The heads directory stores a file for each local branch in your local repository.  storing the latest commit on each branch. 
<!-- TOC --><a name="unmodified-and-modified-files"></a>
#### Unmodified and Modified Files
**Tracked files** in the **working directory** can be in one of **two states**. 
- _Unmodified files_ are files in the working directory that have not been edited since the last commit.
- Once a file in the working directory has been edited, it becomes a _modified file_.

1. `colors` is an unmodified file. It is not yet listed in the git status output.
```shell
myProject$ git status
On branch main
nothing to commit, working tree clean
```
2. Edit `colors` file
```shell
myProject$ echo "orange is the second color" >> colors
```
3. `colors` file is a modified file, It is now listed in the git status output -  it has not been added to the staging area.
```shell
myProject$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   colors

no changes added to commit (use "git add" and/or "git commit -a")
```
4. add the `colors` file to the staging area so that it can be included in your next commit, and you will observe how the git status output changes.
```shell
myProject$ git add colors
myProject$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   colors
```
5. Making Commit
```shell
myProject$ git commit -m "orange"
```
```shell
myProject$ git log
commit f1c737bec966364ebb7355038908946dc024394f (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red

```

![orangecommit](/img/gitArticles/orangecommit.png)

This blue arrow represents the **parent link**. Every commit, other than the very first one in a repository, has a parent commit (some commits can have more than one parent).
<!-- TOC --><a name="creating-a-branch"></a>
### Creating a Branch
>`git branch` List local branches 
>`git branch <new_branch_name>` Create a branch

1. check our branches
```shell
myProject$ git branch 
* main
```
2. create a branch called `feature`
```shell
myProject$ git branch feature
```
3. check our repository log
```shell
myProject$ git log
commit f1c737bec966364ebb7355038908946dc024394f (HEAD -> main, feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red
```
->  A new branch called feature that points to the orange commit.
4. check refs directory
```shell
/myProject$ cat .git/refs/heads/feature 
f1c737bec966364ebb7355038908946dc024394f
/myProject$ cat .git/refs/heads/main 
f1c737bec966364ebb7355038908946dc024394f
```
->`refs/heads/` directory contains two files now(our branches).
->both branches point to the same commit (orange commit).
![](/img/gitArticles/featurebranch.png)

<!-- TOC --><a name="what-is-head-main"></a>
### what is `HEAD —> main`
If you run `git log` you notice there is `HEAD —> main` in the parentheses next to the orange commit.
```shell
myProject$ git log
commit f1c737bec966364ebb7355038908946dc024394f (HEAD -> main, feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red
    
```
-> HEAD is a pointer that tells you which branch you are on

If you open the `HEAD` file in the `local repository` directory
```shell
myProject$ cat .git/HEAD 
ref: refs/heads/main
```
->The `HEAD` file contains ref: refs/heads/main, which is a reference to the main file that represents the main branch.
<!-- TOC --><a name="switching-branches"></a>
### Switching Branches
>`git switch <branch_name>` Switch branches
>`git checkout <branch_name>` Switch branches

Another way of knowing which branch you’re currently
```shell
myProject$ git branch 
  feature
* main #the branch you are currently on will have an asterisk next to it.
```

The git switch (or git checkout) command does three things when used to switch branches:
1. It changes the `HEAD` pointer to point to the branch you are switching onto.
2. It populates the `staging area` with a snapshot of the commit you are switching onto. 
3. It copies the contents of the `staging area` into the `working directory`.

```shell
myProject$ git switch feature 
Switched to branch 'feature'

myProject$ git log
commit f1c737bec966364ebb7355038908946dc024394f (HEAD -> feature, main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red
    
myProject$ cat .git/HEAD 
ref: refs/heads/feature
```
-> `HEAD` points to the feature branch
-> The contents of the `HEAD` switch from the main branch onto the feature branch

![](/img/gitArticles/headfeature1.png)
<!-- TOC --><a name="working-on-a-separate-branch"></a>
### Working on a Separate Branch
Now as we are in the feature branch lets edit the `colors` file and make a `commit`
1. modify `colors` file
```shell
myProject$ echo "Yellow is the third" >> colors
```
2. add the file to the `staging area`
```shell
myProject$ git add colors
```
3. make a commit
```shell
myProject$ git commit -m "yellow"
[feature 5af3853] yellow
 1 file changed, 1 insertion(+)
```
4. check our repository log
```shell
myProject$ git log
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow

commit f1c737bec966364ebb7355038908946dc024394f (main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red
```
-> The `feature` branch points to the latest commit, the **yellow commit**. 
-> The `main` branch still points to the **orange commit**.
![](/img/gitArticles/yellowcommit.png)

<!-- TOC --><a name="git-protects-you-from-losing-uncommitted-changes"></a>
### Git protects you from losing uncommitted changes

1. check our repository status
```shell
myProject$ git status
On branch feature
nothing to commit, working tree clean
```
2. add some text to `colors` file
```shell
myProject$ echo "Green is the fourth color" >> colors
```
3. check our repository status again
```shell
myProject$ git status
On branch feature
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   colors

no changes added to commit (use "git add" and/or "git commit -a")
```
-> We have a modified file but we are going to switch without committing the change
4. switch to `main` branch
```shell
myProject$ git switch main
error: Your local changes to the following files would be overwritten by checkout:
	colors
Please commit your changes or stash them before you switch branches.
Aborting
```
-> Git _does not_ allow us to switch branches
5. remove the new line from `colors`
6. check our repository status
```shell
myProject$ git status
On branch feature
nothing to commit, working tree clean
```
<!-- TOC --><a name="switching-branches-changes-files-in-the-working-directory"></a>
### Switching branches changes files in the working directory
1. lets check what branch we are on now
```shell
myProject$ git branch 
* feature
  main
```
-> we are on the `feature` branch
2. lets check the contents of the `colors` file
```shell
myProject$ cat colors 
red is the first color
orange is the second color
Yellow is the third
```
3. now lets switch the the `main` branch
```shell
myProject$ git switch main 
Switched to branch 'main'

```
4. lets check the contents of the `colors` file again
```shell
myProject$ cat colors 
red is the first color
orange is the second color
```
->  the `colors` file content **before** you switched onto the `main` branch mentioned the colors **red, orange, and yellow.** The version of the file **after** you switch onto the `main` branch mentions **only** the colors **red and orange.**
5. lets check the log
```shell
myProject$ git log 
commit f1c737bec966364ebb7355038908946dc024394f (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red
```
-> log output shows only the `red` and `orange` commits. It no longer shows the `yellow` commit.
6. View the list of all commits
```shell
myProject$ git log --all
commit 5af38538b289883f1914bacf6d65130c0a87c80a (feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow

commit f1c737bec966364ebb7355038908946dc024394f (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red

```
<!-- TOC --><a name="merging"></a>
## Merging
_Merging_ in Git is one way you can integrate the changes made in one branch into another branch. In any merge, there is one branch that you are merging, called the _source branch_, and one branch that you’re merging into, called the _target branch_.
The target branch is the branch that receives the changes and is therefore the only one that is altered in this operation.
<!-- TOC --><a name="types-of-merges"></a>
### Types of Merges 
There are two types of merges: 
<!-- TOC --><a name="fast-forward-merges"></a>
#### Fast-forward merges
Occurs when the development histories of the branches involved in the merge **have not diverged**(it is possible to reach the target branch by following the parent links that make up the commit history of the source branch).
-> Git takes the pointer of the target branch and moves it to the commit of the source branch

Suppose there are two branch in my Book project repository, main branch and _chapter_six_ branch
![](/img/gitArticles/fastforward1.png)
If I follow the parent links from the _chapter_six_ branch, which points to commit `E`, backward, I reach the main branch, which points to commit `B`. Therefore, the main branch and the _chapter_six_ branch have **not diverged**.
If I were to now **merge the _chapter_six_ branch into the main branch**, a _fast-forward merge_ would occur. During the fast-forward merge, the main branch pointer would move forward to point to the commit that the _chapter_six_ branch points to, which is commit `E`,
![](/img/gitArticles/fastforward2.png)
<!-- TOC --><a name="three-way-merge"></a>
#### three-way merge

There is no way to follow the parent links  of the _chapter_eight_ branch backward to reach the commit that the `main` branch points to, which is commit `L`. We say that the development histories of the branches **_have diverged_**.
![](/img/gitArticles/threeway1.png)
If I merge the _chapter_eight_ branch into the `main` branch, it **can’t be a fast-forward merge**. Instead, a _merge commit_ (represented by commit M) will be created to tie the two development histories together. A merge commit is a commit that has more than one parent.
![](/img/gitArticles/threeway2.png)
More about it is coming later.
<!-- TOC --><a name="doing-a-fast-forward-merge"></a>
### Doing a Fast-Forward Merge
In our `local repository` we have:
![](/img/gitArticles/yellowcommit.png)
The feature branch is **the source branch** and the main branch is the **target branch.** You can see that you **can reach the main branch** by following the commit history of the feature branch; therefore, this will be a fast-forward merge.

There are two steps involved in doing a merge: 
1. Switch onto the branch that you want to merge into (the target branch).
2. Use the git merge command and pass in the name of the branch you’re merging (the source branch).
>`git merge <branch_name>` Integrate changes from one branch into another branch.

<!-- TOC --><a name="execute-a-merge"></a>
#### EXECUTE A MERGE
1. switch to the `main` branch
```shell
myProject$ git switch main
```
2. check the contents of the `colors` file
```shell
myProject$ cat colors 
red is the first color
orange is the second color
```
3. merge the `feature` branch
```shell
myProject$ git merge feature 
Updating f1c737b..5af3853
Fast-forward
 colors | 1 +
 1 file changed, 1 insertion(+)
```
4. check the contents of the `colors` file again
```shell
myProject$ cat colors 
red is the first color
orange is the second color
Yellow is the third
```
5. check the repository log
```shell
myProject$ git log
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> main, feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow

commit f1c737bec966364ebb7355038908946dc024394f
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red

```
-> log output shows that `main` points to the `yellow` commit.

<!-- TOC --><a name="checking-out-commits"></a>
### Checking Out Commits
At the moment, you are on the main branch, which points to the yellow commit. But what if you want to look at an older version of your project?
>`git checkout <commit_hash>` Check out a commit.

the HEAD pointer will point directly to a commit instead of pointing to a branch. This means that you will be in something that Git (scarily) calls _detached_ HEAD _state_.
->It is not recommended to make any changes to a repository while in detached HEAD state

1. view commits hashes
```shell
myProject$ git log --all
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> main, feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow

commit f1c737bec966364ebb7355038908946dc024394f
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

commit 335bd5d9d73162eb884f2867979550be978430b8
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 01:35:04 2023 +0100

    red

```
2. lets go to the first commit, `red` commit
```shell
myProject$ git checkout 335bd5d9d73162eb884f2867979550be978430b8
Note: switching to '335bd5d9d73162eb884f2867979550be978430b8'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 335bd5d red
```
3. view `colors` file content
```shell
myProject$ cat colors 
red is the first color
```
4. back to `main` branch
```shell
myProject$ git checkout main 
Previous HEAD position was 335bd5d red
Switched to branch 'main'
```
5. the `colors` file content
```shell
myProject$ cat colors 
red is the first color
orange is the second color
Yellow is the third

```
<!-- TOC --><a name="creating-a-branch-and-switching-onto-it-in-one-go"></a>
### Creating a Branch and Switching onto It in One Go
>`git switch -c <new_branch_name>` Create a new branch and switch onto it
>`git checkout -b <new_branch_name>` Create a new branch and switch onto it

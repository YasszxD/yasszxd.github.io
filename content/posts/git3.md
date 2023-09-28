---
title: "Git III: Remote Repository 1"
date: 2023-08-23T14:04:23+02:00
draft: false
toc: false
images:
tags:
  - git
---
---
<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Why Do We Use Remote Repositories?](#why-do-we-use-remote-repositories)
- [Setting Up Authentication Credentials](#setting-up-authentication-credentials)
- [The Two Ways to Start Work on a Git Project](#the-two-ways-to-start-work-on-a-git-project)
   * [STARTING FROM A LOCAL REPOSITORY](#starting-from-a-local-repository)
   * [STARTING FROM A REMOTE REPOSITORY](#starting-from-a-remote-repository)
- [Creating a Remote Repository with Data](#creating-a-remote-repository-with-data)
   * [CREATING THE REMOTE REPOSITORY](#creating-the-remote-repository)
   * [ADDING A CONNECTION TO THE REMOTE REPOSITORY](#adding-a-connection-to-the-remote-repository)
   * [REMOTE BRANCHES AND REMOTE-TRACKING BRANCHES](#remote-branches-and-remote-tracking-branches)
   * [PUSHING TO A REMOTE REPOSITORY](#pushing-to-a-remote-repository)
      + [Making a push](#making-a-push)
- [Cloning and Fetching](#cloning-and-fetching)
   * [WHAT IS ORIGIN/HEAD?](#what-is-originhead)
   * [CLONING REPOSITORIES AND DIFFERENT TYPES OF BRANCHES](#cloning-repositories-and-different-types-of-branches)
   * [THE ORIGIN SHORTNAME?](#the-origin-shortname)
   * [Deleting Branches](#deleting-branches)
   * [MAKING A COMMIT IN THE LOCAL REPOSITORY](#making-a-commit-in-the-local-repository)
   * [PUSHING TO THE REMOTE REPOSITORY](#pushing-to-the-remote-repository)
   * [FETCHING CHANGES FROM THE REMOTE REPOSITORY](#fetching-changes-from-the-remote-repository)
   * [INTEGRATING CHANGES INTO A LOCAL BRANCH](#integrating-changes-into-a-local-branch)
      + [Deleting Branches](#deleting-branches-1)

<!-- TOC end -->
---

There is two types of repositories: _local repositories_ and _remote repositories_. _Local repositories_ are found on a computer, while _remote repositories_ are hosted on a hosting service in the cloud.
There is three main Git hosting services:
- GitHub
- GitLab
- Bitbucket
<!-- TOC --><a name="why-do-we-use-remote-repositories"></a>
## Why Do We Use Remote Repositories?
- Easily back up your project somewhere other than your computer.
- Access a Git project from multiple computers.
- Collaborate with others on Git projects.
<!-- TOC --><a name="setting-up-authentication-credentials"></a>
## Setting Up Authentication Credentials
To transfer data between a local repository and a remote repository on a hosting service, you must connect and authenticate using either **SSH** or **HTTPS**.
In this example, we will use **GitHub** and authenticate using **SSH**.

```shell
$ ssh-keygen -t ed25519 -C "my_email@example.com"
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_ed25519
$ #copy the contents of `id_ed25519.pub into Github as an SSH key.
```

![](sshkey.png)
```shell
$ ssh -T git@github.com #to test
$ > Hi YasszxD! You've successfully authenticated, but GitHub does not provide shell access.
```

<!-- TOC --><a name="the-two-ways-to-start-work-on-a-git-project"></a>
## The Two Ways to Start Work on a Git Project
<!-- TOC --><a name="starting-from-a-local-repository"></a>
### STARTING FROM A LOCAL REPOSITORY
To start to work on a Git project from a local repository, you must first create a local repository on a computer using the git init command and make at least one commit. Next, you must create a remote repository on a hosting service. Finally, you may upload data from the local repository to the remote repository.
<!-- TOC --><a name="starting-from-a-remote-repository"></a>
### STARTING FROM A REMOTE REPOSITORY
To start to work on a Git project from a remote repository, you can either find a remote repository that you want to work on or create a new remote repository on a hosting service. Then, you _clone_ (in other words, copy) the remote repository to your computer, which will create a local repository.

<!-- TOC --><a name="creating-a-remote-repository-with-data"></a>
## Creating a Remote Repository with Data
1. Create the remote repository on the hosting service. 
2. Add a connection to the remote repository in the local repository. 
3. Upload (or push) data from the local repository to the remote repository.
<!-- TOC --><a name="creating-the-remote-repository"></a>
### CREATING THE REMOTE REPOSITORY
![](newrep.png)
![](createrep.png)

-> we created a remote repository called myProject-remote; however, there is no data in the remote repository.
<!-- TOC --><a name="adding-a-connection-to-the-remote-repository"></a>
### ADDING A CONNECTION TO THE REMOTE REPOSITORY
A local repository can communicate with a remote repository when the local repository has a connection to the remote repository stored within it. This connection will have a name, which we refer to as the _remote repository shortname_.
>`git remote add <shortname> <URL>` Add a connection to a remote repository named `<shortname>` at `<URL>`
>`git remote` List the remote repository connections stored in the local repository by shortname
>`git remote -v` List the remote repository connections in the local repository with _shortnames_ and _URLs_

1. List the remote repository connections stored in the local repository
```shell
myProject$ git remote #no output means no remote connection
# also cat .git/config there is no mentions of your remote repository URL or shortname in the file.
```
2. add remote repository connection
```shell
myProject$ git remote add origin git@github.com:YasszxD/myProject-remote.git # default connection name is origin
```
3. List the remote repository connections stored in the local repository again
```shell
yexz@debian:~/Desktop/myProject$ git remote
origin
```
List connection with shortnames and URLs
```shell
myProject$ git remote -v
origin	git@github.com:YasszxD/myProject-remote.git (fetch)
origin	git@github.com:YasszxD/myProject-remote.git (push)

```
Also there is one connection to a remote repository listed in the config file.
```shell
myProject$ cat .git/config 
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = git@github.com:YasszxD/myProject-remote.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```
-> The rainbow-remote repository still does not have any data in it
<!-- TOC --><a name="remote-branches-and-remote-tracking-branches"></a>
### REMOTE BRANCHES AND REMOTE-TRACKING BRANCHES
When you push a local branch to a remote repository, you will create a _remote branch_. A remote branch is a branch in a remote repository.
Remote branches _do not_ automatically update when you make more commits on local branches. You have to explicitly push commits from a local branch to a remote branch.
Every remote branch (that a local repository knows about) also has a **_remote-tracking branch_**, it is a **reference** in a local repository to the **commit** a remote branch **pointed at the last time any network communication happened** with the remote repository.
> ⛔ekher commit pointet 3liha l remote branch wa9t 3amlo ekher cnx, yesta3mloha bech ta3rah l ama branch tpushi w mn ama branch tpulli⛔
<!-- TOC --><a name="pushing-to-a-remote-repository"></a>
### PUSHING TO A REMOTE REPOSITORY
>`git push <shortname> <branch_name>` Upload content from `<branch_name>` to the `<shortname>` remote repository

After you execute the git push command, two things will happen: 
1. A remote branch will be created in your remote repository.
2. A remote-tracking branch will be created in your local repository.

<!-- TOC --><a name="making-a-push"></a>
#### Making a push
1. lets check the refs directory in the local repository
```shell
myProject$ ls .git/refs/
heads  tags
```
2. push `main` branch to the remote repository
```shell
myProject$ git push origin main
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 12 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (9/9), 688 bytes | 688.00 KiB/s, done.
Total 9 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), done.
To github.com:YasszxD/myProject-remote.git
 * [new branch]      main -> main
```
3.  look at the directories that are inside the refs directory again
```shell
myProject$ ls .git/refs/
heads  tags  remotes
# Inside the remotes directory is a directory called origin, and inside this directory is a file called main. This represents the new origin/main remote-tracking branch.
```
4. in the remote repository the new branch is added
![](push1.png)
5. list all the branches
```shell
myProject$ git branch --all 
  feature
* main
  remotes/origin/main
```
6. the log
```shell
myProject$ git log
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> main, origin/main, feature)
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
7. add `feature` branch
```shell
myProject$ git switch feature
Switched to branch 'feature'

myProject$ git push origin feature
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
remote: 
remote: Create a pull request for 'feature' on GitHub by visiting:
remote:      https://github.com/YasszxD/myProject-remote/pull/new/feature
remote: 
To github.com:YasszxD/myProject-remote.git
 * [new branch]      feature -> feature

myProject$ git branch --all
* feature
  main
  remotes/origin/feature
  remotes/origin/main
  
myProject$ git log
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> feature, origin/main, origin/feature, main)
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
<!-- TOC --><a name="cloning-and-fetching"></a>
## Cloning and Fetching
the term _clone_ or _cloning_ to refer to the process of copying a remote repository onto a computer to create a local repository
>`git clone <URL> [directory_name]` Clone a remote repository

The git clone command does the following:
1. Create a project directory inside the current directory. 
2. Create (initialize) the local repository.
3. Download all the data from the remote repository. 
4. Add a connection to the remote repository that was cloned; by default it will have the shortname origin in the new local repository.

```shell
Desktop$ git clone git@github.com:YasszxD/myProject-remote.git friendProject
Cloning into 'friendProject'...
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 9 (delta 1), reused 9 (delta 1), pack-reused 0
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (1/1), done.
```
-> I cloned the remote repository onto your computer and created a second local repository called friendProject.

```shell
friendProject$ git remote -v
origin	git@github.com:YasszxD/myProject-remote.git (fetch)
origin	git@github.com:YasszxD/myProject-remote.git (push)

friendProject$ git branch --all
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/feature
  remotes/origin/main
  
friendProject$ git log
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> main, origin/main, origin/feature, origin/HEAD)
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
->the origin remote repository shortname is already listed.
->git branch output shows a pointer called origin/HEAD that points to the origin/main remote-tracking branch, and that you have an origin/feature remote-tracking branch
->there is no local feature branch.
<!-- TOC --><a name="what-is-originhead"></a>
### WHAT IS ORIGIN/HEAD?
When you clone a repository, Git needs to know which branch it should be on when it’s done cloning. The origin/HEAD pointer determines which branch this is.
<!-- TOC --><a name="cloning-repositories-and-different-types-of-branches"></a>
### CLONING REPOSITORIES AND DIFFERENT TYPES OF BRANCHES
In the friendProject repository there is **no reference** to the **local feature branch**. However, there is a reference to the **origin/feature remote-tracking branch**. This is because when you clone a repository the git clone command will **create** **remote-tracking branches** for all the **branches currently present in the remote repository** that is being cloned, but the only local branch that is created is the branch that origin/HEAD points to. 

To work on the **feature branch**, they must **switch onto it**. Then Git will create a local feature branch **based on where the remote-tracking branch** was pointing.

>main was created because  ORIGIN/HEAD points to it.

```shell
friendProject$ git branch --all 
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/feature
  remotes/origin/main
  
friendProject$ git switch feature 
branch 'feature' set up to track 'origin/feature'.
Switched to a new branch 'feature'

friendProject$ git branch --all 
* feature
  main
  remotes/origin/HEAD -> origin/main
  remotes/origin/feature
  remotes/origin/main
```
<!-- TOC --><a name="the-origin-shortname"></a>
### THE ORIGIN SHORTNAME?
it was directly **cloned** from a remote repository. **At the time of cloning** the **remote repository URL** was **associated with a shortname** in the local repository, and **origin is the default shortname** Git associates with a remote repository when you clone it.
<!-- TOC --><a name="deleting-branches"></a>
### Deleting Branches
>`git push <shortname> -d <branch_name>` Delete a remote branch and the associated remote-tracking branch
>`git branch -d <branch_name>` Delete a local branch

```shell
friendProject$ git branch --all 
* feature
  main
  remotes/origin/HEAD -> origin/main
  remotes/origin/feature
  remotes/origin/main
  
friendProject$ git push origin -d feature 
To github.com:YasszxD/myProject-remote.git
 - [deleted]         feature
```
![](branch1.png)
```shell
friendProject$ git branch --all 
* feature
  main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  
friendProject$ git switch main 
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

friendProject$ git branch -d feature 
Deleted branch feature (was 5af3853).

friendProject$ git branch --all 
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main

```
-> The fact that our friend deleted the feature and origin/feature branches in their friendProject repository does not affect our myProject repository.
<!-- TOC --><a name="making-a-commit-in-the-local-repository"></a>
### MAKING A COMMIT IN THE LOCAL REPOSITORY
```shell
friendProject$ echo "Green is the fourth" >> colors

friendProject$ git add colors 

friendProject$ git commit -m "green"
[main b177438] green
 1 file changed, 1 insertion(+)
yexz@debian:~/Desktop/friendProject$ git log 
commit b177438a693f8c76426bb7198e46f270441e811b (HEAD -> main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 20:58:48 2023 +0100

    green

commit 5af38538b289883f1914bacf6d65130c0a87c80a (origin/main, origin/HEAD)
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
-> The green commit won’t be in GitHub yet. +> because remote repositories do not update automatically
-> The local main branch has updated to point to the green commit.
-> The origin/main remote-tracking branch still points to the yellow commit.

![](push2.png)
1. The local repository with the changes has to explicitly push those changes to a remote repository.
2. The local repository without the changes has to explicitly fetch and integrate the changes from the remote repository.
<!-- TOC --><a name="pushing-to-the-remote-repository"></a>
### PUSHING TO THE REMOTE REPOSITORY
Git needs to know which remote branch you want to push to.
- If the local branch has an upstream branch defined for it, you can use `git push` **with no arguments** and Git will automatically push the work to that branch. However
- if no upstream branch is defined for the local branch you’re working on, **you’ll need to specify which remote branch to push** to when you enter the `git push` command.

>`git branch -vv` List the local branches and their upstream branches, if they have any

```shell
friendProject$ git branch --all
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  
friendProject$ git branch -vv
* main b177438 [origin/main: ahead 1] green

friendProject$ git status 
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)
nothing to commit, working tree clean
```

Now lets push the updates
```shell
friendProject$ git push 
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 280 bytes | 280.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:YasszxD/myProject-remote.git
   5af3853..b177438  main -> main
   
friendProject$ git status 
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean

friendProject$ git log 
commit b177438a693f8c76426bb7198e46f270441e811b (HEAD -> main, origin/main, origin/HEAD)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 20:58:48 2023 +0100

    green

commit 5af38538b289883f1914bacf6d65130c0a87c80a
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow
```
-> the **origin/main remote-tracking branch** has updated to point to the green commit.
-> the **remote main branch** has been updated to point to the green commit.
![](push3.png)
![](push4.png)
<!-- TOC --><a name="fetching-changes-from-the-remote-repository"></a>
### FETCHING CHANGES FROM THE REMOTE REPOSITORY
 The term _fetch_ or _fetching_ to refer to the process of downloading data from a remote repository to a local repository.
 >`git fetch <shortname>` Download data from the `<shortname>` remote repository
 >`git fetch` Download data from the remote repository with shortname origin

-> The `git fetch` command **affects only** remote-tracking branches. It **does not affect local branches**.
```shell
myProject$ git log --all
commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> feature, origin/main, origin/feature, main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow

commit f1c737bec966364ebb7355038908946dc024394f
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 02:33:23 2023 +0100

    orange

myProject$ git fetch 
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), 260 bytes | 260.00 KiB/s, done.
From github.com:YasszxD/myProject-remote
   5af3853..b177438  main       -> origin/main
   
myProject$ git log --all
commit b177438a693f8c76426bb7198e46f270441e811b (origin/main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 20:58:48 2023 +0100

    green

commit 5af38538b289883f1914bacf6d65130c0a87c80a (HEAD -> feature, origin/feature, main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow
```
-> The origin/main remote-tracking branch is **pointing to the green commit**.
-> The local main branch is **still pointing to the yellow commit**.
![](push5.png)

<!-- TOC --><a name="integrating-changes-into-a-local-branch"></a>
### INTEGRATING CHANGES INTO A LOCAL BRANCH
Git provides two ways to integrate changes: _merging_ and _rebasing_, we will use merge here(rebasing coming later).
We are going to merge the origin/main remote-tracking branch into the local main branch in the rainbow repository.
This will be a fast-forward merge.
```shell
myProject$ git switch main
Switched to branch 'main'

myProject$ git merge origin/main
Updating 5af3853..b177438
Fast-forward
 colors | 1 +
 1 file changed, 1 insertion(+)
 
myProject$ git log
commit b177438a693f8c76426bb7198e46f270441e811b (HEAD -> main, origin/main)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 20:58:48 2023 +0100

    green

commit 5af38538b289883f1914bacf6d65130c0a87c80a (origin/feature, feature)
Author: Yexz <abdelhedi166@gmail.com>
Date:   Sun Sep 24 13:46:47 2023 +0100

    yellow


```
->The git log output indicates that the local main branch points to the green commit.
![](push6.png)
<!-- TOC --><a name="deleting-branches-1"></a>
#### Deleting Branches
In myProject repository, we still have the local feature branch and the origin/feature remote-tracking branch.
> `git fetch -p` Remove remote-tracking branches that correspond to deleted remote branches and download data from the remote repository

```shell
myProject$ git branch --all
  feature
* main
  remotes/origin/feature
  remotes/origin/main
  
myProject$ git fetch -p
From github.com:YasszxD/myProject-remote
 - [deleted]         (none)     -> origin/feature

myProject$ git branch -d feature 
Deleted branch feature (was 5af3853).

myProject$ git branch --all
* main
  remotes/origin/main
```

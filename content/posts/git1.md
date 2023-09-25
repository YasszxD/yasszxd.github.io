---
title: "Git I: Git introduction"
date: 2023-08-23T14:04:23+02:00
draft: false
toc: false
images:
tags:
  - git
---
---
<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [What Is Git?](#what-is-git)
- [Why Use Git?](#why-use-git)
   * [Collaboration ](#collaboration)
      + [History Tracking](#history-tracking)
- [Getting started with Git](#getting-started-with-git)
   * [Installing Git](#installing-git)
   * [Setting some Git Configurations](#setting-some-git-configurations)

<!-- TOC end -->
---

<!-- TOC --><a name="what-is-git"></a>
## What Is Git?
Git is a powerful and widely-used **version control system** that plays a crucial role in modern software development. It was created by Linus Torvalds in 2005 and has since become an integral part of the development workflow for individuals and teams.
![Git](/img/gitArticles/gitLogo.png)
<!-- TOC --><a name="why-use-git"></a>
## Why Use Git?
<!-- TOC --><a name="collaboration"></a>
### Collaboration 
Git facilitates collaboration among developers. It allows multiple developers to work on the same project simultaneously without interfering with each other's work.
<!-- TOC --><a name="history-tracking"></a>
#### History Tracking
Git keeps a detailed history of every change made to the project. This history includes who made the changes, what changes were made, and when they were made.
<!-- TOC --><a name="getting-started-with-git"></a>
## Getting started with Git
<!-- TOC --><a name="installing-git"></a>
### Installing Git
To install Git you can download it from the [official Git website](https://git-scm.com/downloads), or run this command on Debian-based Linux distros.
```shell
$ sudo apt install git
```

To check weather the installation successfully completed: 
```shell
$ git version
# output: git version 2.39.2
```
<!-- TOC --><a name="setting-some-git-configurations"></a>
### Setting some Git Configurations
Git configurations are settings that allow you to customize how Git works. To work with Git, you must set a few configuration variables related to user settings.
```shell
git config --global user.name <name>
git config --global user.email <email>
```

To check if the configurations are validated:
```shell
git config --global --list 
# Output:
# user.name=Yexz
# user.email=abdelhedi166@gmail.com
```


---
title : "Git学习笔记"
author : "Jun"
date : "2021-02-28T15:38:15+08:00"
categories : ["Linux"]
tags : ["Linux"]
---

## Git简介

## Git基本概念

### 工作区

工作区顾名思义是我们进行工作的地方，我们在其中对文件进行修改。所有未加入暂存区的文件都属于工作区。

### 暂存区

暂存区

### 仓库

仓库是Git中最重要的部分，


## 基本操作

### 创建Git仓库

创建Git仓库主要有两种方式，一是从零开始创建一个新的仓库，二是获取一个已经存在的仓库。

对于第一种方式，我们只要在任一目录下运行`git init`就可以了，Git会给我们以下提示：

```
Initialized empty Git repository in /path/to/repo/.git/
```

这就表示我们成功在`/path/to/repo/`目录下创建了一个仓库，其中所有Git保存的关键信息都在`.git`中，所以请勿在不确定的情况下修改或删除此目录！

对于第二中方式，我们只要知道目标repo的地址，使用`git clone <url>`就可以了。比如我们尝试clone在github下的一个仓库：

```
git clone https://github.com/torvalds/linux.git
```
这样我们就可以把linux内核的代码拉取到本地了。

### 使用Git

#### 提交内容到仓库

假设我们现在已经创建了一个空的Git仓库，想要写一些东西。我们首先创建了一个文件叫`README.txt`。

这个文件现在属于工作区，因为我们没有使用Git去记录它的修改历史。我们可以使用`git status`查看当前仓库的状态：

```
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.txt

nothing added to commit but untracked files present (use "git add" to track)

```

Git会温馨地提示我们`README.txt`是Untracked files，可以使用`git add <file>`来加入暂存区。

当我们使用`git add README.txt`后，再尝试使用`git status`，显示：
```
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   README.txt

```

此时Git告诉我们，`README.txt`已经加入了暂存区，但是还没有提交到仓库中。

`git add` 是可以处理多个文件的，比如`git add foo.cc bar.cc`，也可以使用`git add .`将所有文件一起加入暂存区。

当我们我们准备好了，我们可以使用`git commit`将暂存区的所有文件提交到仓库了。之后Git会要求你写一个commit message来表明你此次提交内容的简介。你也可以使用`git commit -m "commit message here..."`来直接提交。

举个例子，这里我们使用`git commit -m "add README.txt"`来提交我们的内容。此时，Git会显示：
```
[master (root-commit) 8612de5] add README.txt
 1 file changed, 1 insertion(+)
 create mode 100644 README.txt

```

其中，`8612de5`是本次提交的hash值，未来我们可以通过它回退到这个提交。我们还可以使用`git show <hash>`来显示指定提交的内容。这里我们可以使用`git show`直接显示当前提交的内容。

#### 修改提交

##### 在工作区想要取消更改

```
git checkout -- <file>
```

##### 加入了暂存区，想要退到工作区

```
git restore --staged <file>
```

##### 已经提交了，想要删除此提交

```
git reset --hard HEAD^
```

##### 已经提交了，想要修改此提交

```
git commit --amend
```
此时Git会要求你重新写commit message。如果你想在此提交中加入更多文件，请在此命令前使用`git add <file>`。
它的本质是把之前的提交重新放回暂存区，使我们可以重新修改。

#### 查看变更内容

##### 未暂存的

```
git diff
```
此命令比较的是工作区文件与暂存区快照的区别

##### 已暂存的

```
git diff --staged
```

此命令比较的是暂存区内容与上一次提交的区别。

#### 查看提交历史

```
git log
```

## 使用远程库 

## Git分支

在此之前，我们对Git的所有操作都是在一个分支，即master分支上进行的。这在单人开发下问题不大，但如果我们是多人协作时，可能会遇到以下问题：
- q1
- q1
- q2

### 创建分支

```
git checkout -b <branch-name>
```

这会创建一个新的分支并切入此分支。

### 列出全部分支

```
git branch
```

### 删除分支

```
git branch -D <branch-name>
```

### 合并分支

```
git merge <branch-name>
```

这会把指定分支合并到当前分支上，一般是master

#### 冲突



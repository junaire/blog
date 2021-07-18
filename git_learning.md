---
title : "Git 常用命令总结"
author : "Jun"
date : "2021-02-28T15:38:15+08:00"
categories : ["Linux"]
tags : ["Linux"]
---
`git`是一个强大的版本管理工具，在日常开发中有着诸多的用途，这里记录下常用的命令．
#### 创建版本库

```bash
mkdir learning
cd learning
git init
```

#### 将文件添加到版本库

```bash
git add demo.txt
```

#### 将文件提交到仓库

```bash
git commit -m "messages..."
```

#### 查看仓库当前状态

```bash
git status
```

#### 查看文件修改内容

```bash
git diff demo.txt #在文件修改后，提交前
```

#### 查看文件历史记录

```bash
git log
```

#### 版本回退

```
git reset --hard HEAD^ #回退到上一版本
git reset --hard COMMIT_ID #回退到指定版本
```

#### 查看命令历史

```bash
git reflog
```

#### 撤销修改

```bash
git checkout -- FILE_NAME # 撤销文件在工作区的所有修改 在使用git add 前
```

#### 撤销暂存区的修改

```bash
git reset HEAD file #将暂存区的修改回退到工作区 在使用git add 后
```

#### 删除文件

```bash
git rm FILE_NAME
git commit -m "message..."
```

#### 添加远程库

```bash
git remote add origin git@github.com:nailu0/learning.git
```

#### 推送内容

```bash
git push -u origin matser
```

#### 克隆远程库

```bash
git clone git@github.com:nailu0/learning.git
```

#### 删除远程库并重新关联

```git
git remote rm origin
git remote add origin git@github.com:nailu0/learning.git
```

#### 创建分支并切换

```
git checkout -b dev
#等价于
git branch dev
git checkout dev
```

#### 合并分支

```
git merge dev
```


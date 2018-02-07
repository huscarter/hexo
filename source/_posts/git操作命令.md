---
title: git操作命令
date: 2017-11-08 06:29:44
categories: android
tags:
     - git
description: 
---
### 查看branch

- 查看本地分支

> git branch

- 查看所有分支

> git branch -a

- 查看远端分支

> git branch -r

### 查看tag

- 查看本地分支

> git tag --list

### 删除分支

- 删除本地分支

> git branch -d feature/v1.1.0

- 删除远端分支

> git branch -r -d origin/feature/v1.0.0

> git push origin :feature/v1.0.0

### 删除tag

- 删除本地tag

> git tag -d v1.0.0

- 删除远端tag

> git tag -d v1.0.0

> git push origin -d tag v1.0.0

- 将本地tags推送到远端

> git push origin --tags

### [官方文档](https://git-scm.com/docs/)


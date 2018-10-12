---
title: git操作命令
date: 2017-11-08 06:29:44
categories: android
tags:
     - git
description: android项目git的常用命令，包括branch和tag操作
---

## branch操作

#### 新建分支
- 
```
git branch v3.0.0
```

#### 查看分支
- 查看本地分支
```
git branch
```

- 查看所有分支
```
git branch -a
```

- 查看远端分支
```
git branch -r
```

#### 删除分支
- 删除本地分支
```
git branch -d feature/v1.1.0
```

- 删除远端分支
```
git branch -r -d origin/feature/v1.0.0

git push origin :feature/v1.0.0
```

#### 拉取\切换分支
```
git checkout v3.0.0
```

#### 获取分支
```
git clone git://github.com/schacon/grit.git [mygrit]
```

#### 提交代码到分支
```
git status // 查看代码的修改状态
git add . // 暂存所有提交的文件
git commit // 提交已暂存的文件
git pull // 先同步代码到本地
git push origin <本地分支名> // 再同步到服务器
```


## tag操作

- 查看本地分支
```
git tag --list
```

- 新建tag
```
git tag v3.0.0
```

- 删除本地tag
```
git tag -d v1.0.0
```

- 删除远端tag
```
git tag -d v1.0.0

git push origin -d tag v1.0.0
```

- 将本地tags推送到远端
```
git push origin --tags
```

### [官方文档](https://git-scm.com/docs/)


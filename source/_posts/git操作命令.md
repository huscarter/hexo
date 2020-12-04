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
```
git branch v3.0.0
```

#### 查看分支
1. 查看本地分支
```
git branch
```

2. 查看所有分支
```
git branch -a
```

3. 查看远端分支
```
git branch -r
```

#### 删除分支
1. 删除本地分支
```
git branch -d feature/v1.1.0
```

2. 删除远端分支
```
git branch -r -d origin/feature/v1.0.0

git push origin -d feature/v1.0.0
```

#### 拉取\切换分支
```
git checkout v3.0.0 // 获取tag
git checkout -b develop origin/develop //获取远端develop分支到本地，本地分支为develop
```

#### 获取分支
```
git clone git://github.com/schacon/grit.git [mygrit]
git clone [-b mygrit] git://github.com/schacon/grit.git
```

#### 提交代码到分支
```
git status // 查看代码的修改状态
git add . // 暂存所有提交的文件
git restore --staged <file> // 放弃修改还原文件
git rm --cached <file> // 删除已经add到暂存区的文件（不会物理删除的，只是变成未add的修改状态）
git commit // 提交已暂存的文件
git pull // 先同步服务器最新代码到本地
git push origin <本地分支名> // 再同步到服务器
```

## 还原代码到指定版本
1. 使用git revert（它是新增记录）
```
git revert -n <commit-id> // 将代码回退倒commit-id时的版本
git commit -m "revert xx 版本" // 提交
git push origin <branch> // 更新服务器版本，此时服务器会多出一条修改记录
```
2. 使用git reset（它会将HEAD移动到你reset的记录）
```
git reset --hand <commit-id> // 将本地代码回退到commit-id时的版本
git push -f// 将远端仓库更新，此时HEAD版本变为commit-id；必须使用-f，因为你本地版本比远端的旧
```

## tag操作
1. 查看本地分支
```
git tag --list
```

2. 新建tag
```
git tag v3.0.0
```

3. 删除本地tag
```
git tag -d v1.0.0
```

4. 删除远端tag
```
git tag -d v1.0.0

git push origin -d tag v1.0.0
```

5. 将本地tags推送到远端
```
git push origin --tags
```

6. 查看本地分支的git地址
```
git remote -v
```

7. 更换本地分支的git地址
```
git remote set-url origin http://xxx.xxx.xxx.git
```

## 分支合并
1. 合并本地分支
```
git merge v1.2.0 --no-commit
```

2. 合并远端分支
```
git merge origin v1.2.0 --no-commit
```

## .git 文件过大操作处理
一般不建议做此操作，因为我们公司不允许使用具有上传功能的网站，所以我们的apk是放到自己的git服务器上
供测试人员下载，久而久之这会导致git的历史记录过大（每一次记录都是apk文件），下面的介绍是用来删除git的历史记录。
```
git filter-branch --index-filter 'git rm -r --cached --ignore-unmatch <文件夹名>'

rm -rf .git/refs/original/
 
git reflog expire --expire=now --all
 
git fsck --full --unreachable
 
git repack -A -d
 
git gc --aggressive --prune=now
 
git push --force

```

### [官方文档](https://git-scm.com/docs/)


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

git push origin -d feature/v1.0.0
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

- 查看本地分支的git地址
```
git remote -v
```

- 更换本地分支的git地址
```
git remote set-url origin http://xxx.xxx.xxx.git
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


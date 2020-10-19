---
title: mac安装jenkins流程
date: 2019-10-31 09:29:44
categories: CI
tags:
     - jenkins
description: jenkins自动部署搞起。
---

### 安装前准备
1. java安装和环境配置
2. git安装
3. Android
4. 安装brew
5. 安装fir-cli
5.1 安装ruby的工具rvm
```
curl -L https://get.rvm.io | bash -s stable
```
5.2 查看可用ruby版本
```
rvm list known
```
5.3 ruby安装
```
rvm install 2.4.0
```
5.4 查看已安装版本(标志安装成功)
```
ruby -v
```
5.5 安装fir-cli
```
sudo gem install -n /usr/local/bin fir-cli
```

### 安装jenkins
```
brew install jenkins
```


### 执行jenkins
```
java -jar /usr/local/Cellar/jenkins/2.202/libexec/jenkins.war --httpPort=8080
```

### 链接launchd配置文件(作用的是在你的当前用户下创建开启启动连接)
```
ln -sfv /usr/local/opt/jenkins/*.plist ~/Library/LaunchAgents
```

### 启动jenkins
```
launchctl load /usr/local/Cellar/jenkins/2.202/homebrew.mxcl.jenkins.plist
```

### 停止jenkins
```
launchctl unload /usr/local/Cellar/jenkins/2.202/homebrew.mxcl.jenkins.plist
```

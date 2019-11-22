---
title: flutter通过jenkins自动化打包
date: 2019-10-31 09:29:44
categories: flutter
tags:
     - flutter
description:
---

### jenkins安装

### jenkins插件安装
#### iOS插件
1. Keychains and Provisioning Profiles Management
2. Xcode integration

#### Android插件

### shell脚本
```
flutter pub get
flutter clean
flutter build apk
fir publish build/app/outputs/apk/release/app-release.apk -T "748dc6f0d79f15d3882913256dbb9d77"
```
### 如果提示command not found
1. 在shell脚本前添加
```
export PATH=$PATH:$FLUTTER_HOME/bin/flutter
export PATH=$PATH:/usr/local/bin/fir
```
2. 在jenkins环境变量前添加PAT，值就是terminal中echo $PATH的值

#### can't find gem fir-cli (>= 0.a) with executable fir
sudo gem install -n /usr/local/bin cocoapods

#### ios 编译提示CocoaPods minimum required version 1.6.0 or greater not installed
```
sudo gem install cocoapods
```

### 提示invalid byte sequence in US-ASCII
在iOS执行脚本前添加，注意后面不要有空格，否则失效
```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

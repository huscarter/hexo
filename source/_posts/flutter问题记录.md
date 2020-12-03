---
title: flutter问题记录
date: 2019-10-18 13:29:44
categories: flutter
tags:
     - flutter
description: Flutter开发遇到的一些问题记录于此，方便后期查看。
---

1. androidstudio 可以开启simulator，但是无法连接

   ```
   sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
   ```
2. brew tap caskroom/versions 命令无反应
  因为该命令过时了，使用下面命令代替

  ```
  brew tap homebrew/cask-versions
  ```
3. 环境变量
  3.1 java

  ```
  export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_231.jdk/Contents/Home
  ```

  3.2 flutter

  ```
  export FLUTTER_HOME=/Users/weihh/Library/Flutter/sdk
  export PATH=$PATH:$FLUTTER_HOME/bin
  ```

  3.3 android

  ```
  export ANDROID_HOME=/Users/weihh/Library/Android/sdk
  export PATH=$PATH:$ANDROID_HOME/platform-tools
  export PATH=$PATH:$ANDROID_HOME/tools
  ```

4. androidstudio设置代理之无法关闭代理

  ```
  配置信息保存在/Users/XXX/.gradle/gradle.properties/文件中删除即可
  ```

5. 一直卡在running pod install
  是因为cocoapod没有安装或者没有更新，执行下面命令

  ```
  brew install cocoapods
  pod setup
  pod repo update
  pod update
  ```

6. BottomNavigationBar每个子tab切换时都会重新initState问题解决

   ```
   body:IndexStack()
   ```
7. ios 编译出现find_spec_for_exe’: can’t find gem cocoapods (>= 0.a) (Gem::GemNotFoundException) from /Library/Ruby/Site/2.3.0/rubygems.rb:299:inactivate_bin_path

   ```
   sudo gem update --system
   gem install cocoapods
   ```

   
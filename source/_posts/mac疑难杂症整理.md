---
title: mac疑难杂症整理
date: 2013-07-23 01:01:01
categories: 技术周边
tags: 
    - 技术周边
description: 记录使用mac开发过程中遇到的各种问题，包括系统的、cli的以及其他一些第三方。
---

## 各种软件的安装

### brew安装

1. 查看是否安装了brew

   ```
   brew -v
   ```

2. 如果没安装跳到[官网](https://brew.sh/index_zh-cn)安装，一般是让你在终端输入如下命令安装。

   ```
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

### npm安装

1. 安装包安装
   
- 直接去[node.js官网]((https://nodejs.org/en/download/))安装node，安装好之后会自动安装
   
2. brew安装

   - 更新brew

     ```
     brew update
     ```

   - 安装node（安装好node，npm就可以用了）

     ```
     brew install node
     ```

   - 查看npm版本

     ```
     npm -v
     ```

### node-sass安装与卸载

1. node安装

   ```
   npm install node-sass -g
   ```

   如果出现错误"EACCES: permission denied, mkdir 使用以下命令安装"，用以下命令

   ```
   sudo npm install node-sass --unsafe-perm --save-dev
   ```

2. node卸载

   ```
   npm uninstall node-sass -g
   ```

   


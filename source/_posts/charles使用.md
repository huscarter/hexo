---
title: charles 使用
date: 2018-08-06 20:29:44
categories: 技术周边
tags:
     - 技术周边
description: 做移动开发时，有时想拦截查看app的请求url信息，这个时候charles神器就展现出了它的用武之地。
---

### 安装 charles
1. [下载地址](https://www.charlesproxy.com/download/]下载好之后直接傻瓜式安装
2. 因为Charles不是从AppStore上下载的应用程序，安装和运行都需要修改系统偏好设置。找到系统设置中的安全与隐私，修改为允许以下位置下载的应用为任何来源。

### 抓包
确保iOS设备iphone或ipad与运行Charles的mac电脑处于同一个wifi环境下

#### 抓取手机网络请求
1. 首先电脑设置为代理，在charles的设置-proxy setting中查看代理的端口号,选中http transparent proxy。
2. 将手机的网络设置为电脑代理，输入电脑的ip和charles proxy的端口号。
3. 用手机走一个网络请求


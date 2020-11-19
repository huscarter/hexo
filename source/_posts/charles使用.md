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
确保Android、iOS设备与运行Charles的mac电脑处于同一个wifi环境下。

#### 抓取手机http请求
1. 首先电脑设置为代理，在charles的设置-proxy setting中查看代理的端口号,选中http transparent proxy。
2. 将macOS proxy关掉（抓电脑端请求的）避免干扰。
3. 将手机的网络设置为电脑代理，输入电脑的ip和charles proxy的端口号。可通过charles-help-local ip address查看电脑ip地址。
4. 如果链接成功charles会有一个弹框获取用户的许可，allow之后用手机走一个网络请求就能看到抓取的请求了。

#### 抓取手机https请求
1. 手机端安装charles的证书，charles就能让手机认为是自己是https请求的服务器。（之后charlse将手机的请求转发到真正的服务器完成请求）；通过下面的操作获取charles证书下载地址
```
charles -> help -> SSL Proxing -> install charles root certificate on a mobile device or remote browser
```
2. 添加charles抓取的https域名
```
charles -> Proxy -> SSL Proxying Settings -> SSL Proxying -> Add // 域名+端口号（443）
```
3. GO!

### 问题
1. charles是收费的，如果试用期到了“每次”只能使用30分钟，之后不能抓包。可以考虑买个。
2. 如果http是乱码需要在配置里配置解码格式，找到安装目录下的info.plist，在vmoption里添加如下代码。
```
<string>-Dfile.encoding=UTF-8</string>
```
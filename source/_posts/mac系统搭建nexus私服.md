---
title: mac系统搭建nexus私服
date: 2018-03-09 10:29:44
categories: android
tags:
     - nexus
     - maven
description: 我们做软件开发，为了方便使用和维护一些自己封装好的组建，把他们放到maven服务器是一个不错的选择。出于安全和版权等考虑，一般公司会搭建私有maven服务器，而nexus私服是一个很好的选择
---

我们做软件开发，为了方便使用和维护一些自己封装好的组建，把他们放到maven服务器是一个不错的选择。出于安全和版权等考虑，一般公司会搭建私有maven服务器，而nexus私服是一个很好的选择

## 下载安装nexus

1. 因为nexus需要JRE环境，所以请先安装好JDK

2. [下载nexus](https://www.sonatype.com/download-oss-sonatype)

3. 选择好自己的版本，解压安装包
 解压之后会出现两个目录：nexus-x.x.x-xx和sonatype-work

4. 配置nexus
4.1 修改nexus-x.x.x-xx/bin/nexus，将INSTALL4J_JAVA_HOME_OVERRIDE的值设置成你的JDK目录。
```
INSTALL4J_JAVA_HOME_OVERRIDE="/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home"
```

## 运行nexus
1. 进入nexus-x.x.x-xx/bin运行命令即可
```
nexus start
```
2. 在浏览器中访问localhost:8081，不报错误即搭建nexus成功。（登录的帐号默认是admin=admin123）

## android发布到nexus私服
 [传送门](../android发布到nexus私服/)

## java发布到私服
待更新
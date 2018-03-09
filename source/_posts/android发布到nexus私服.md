---
title: android发布到nexus私服
date: 2018-03-09 13:29:44
categories: android
tags:
     - maven
description: 为了减少编译时间，将项目中用到的library module做成maven类库，使之可以被其他项目引用
---

## 下载安装nexus

- 因为nexus需要JRE环境，所以请先安装好JDK

- [下载nexus](https://www.sonatype.com/download-oss-sonatype)

- 选择好自己的版本，解压安装包
 解压之后会出现两个目录：nexus-x.x.x-xx和sonatype-work

- 配置nexus

    - 修改nexus-x.x.x-xx/bin/nexus，将INSTALL4J_JAVA_HOME_OVERRIDE的值设置成你的JDK目录。
    ```
    INSTALL4J_JAVA_HOME_OVERRIDE="/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home"
    ```

## 运行nexus

- 进入nexus-x.x.x-xx/bin运行开启命令即可
```
nexus start
```

- 在浏览器中访问localhost:8081，不抱错误即搭建nexus私服成功。登录的帐号默认是admin=admin123

## android发布到私服

[传送门](../android发布到nexus私服/)



## java发布到私服

待定

---
title: 创建JEE项目
date: 2013-12-18 10:56:20
categories: j2ee
tags: 
      - j2ee
description: 初学者，如何创建JEE项目
---

初学者，如何创建JEE项目

### 软件安装：
1. 安装jdk。从官网下载安装包http://www.oracle.com/technetwork/java/javase/downloads/index.html。
1.1 安装jdk时会安装两个文件（jdk和jre），最好安装到不同的目录，请记住他们的安装位置。
1.2 java环境变量配置。
```
JAVA_HOME==你安装jdk的目录（比如：F:\java\jdk）
CLASSPATH==.;%JAVA_HOME%\lib\dt.jar ;
PATH==%JAVA_HOME%\bin;
```

2. 安装tomcat。从官网下载安装包http://tomcat.apache.org/download-60.cgi。
2.1 请记住安装的目录，因为也要配置环境变量。
2.2 tomcat环境变量配置。
```
CATALINA_HOME==你安装tomcat的目录。
PATH==%CATALINA_HOME%\bin;
```

3. 安装eclipse for java EE。从官网下载http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-	developers/keplerr。
3.1 eclipse不用安装，解压即可用。

### 项目创建、导入和部署到tomcat：
1. eclipse 关联tomcat。
1.1 下载tomcat for eclipse plugin，地址http://pan.baidu.com/share/link?shareid=1678904917&uk=1880004551 。
下载的文件里写好了使用方法。
1.2 关联 tomcat。
windows --> preference-->tomcat-->配置里面的 tomcat version、tomcat home（tomcat路径）和tomcat base（tomcat路	劲）三个选项。

2. 新建web 项目。
2.1 打开eclipse-->进入工作空间（WorkSspace）-->右击-->new-->Dynamic Web Project-->...。
2.2 右击你新建的项目-->properties-->tomcat-->choose "is a tomcat project"。
2.3 发布项目，直接点击eclipse工具栏里的start tomcat图标。
2.4 如果项目没有部署到tomcat，可在eclipse-->右击项目-->tomcat tools-->选择你想要的操作。

3、 导入web项目。
3.1 打开eclipse-->进入工作空间（WorkSspace）-->右击-->import-->general project-->选择你的项目。
3.2 发布导入的web项目到tomcat和发布新建的web项目一样。


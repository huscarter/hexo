---
title: java发展史
date: 2016-09-12 06:55:31
categories: j2ee
tags: 
      - java
description: 1991年James Gosling博士领导的Green Project开始启动，目的是开发一种能够在各种消费性电子产品上运行的程序架构。这个计划的产品就是java的前身Oak（橡树）
---

1991年James Gosling博士领导的Green Project开始启动，目的是开发一种能够在各种消费性电子产品上运行的程序架构。这个
计划的产品就是java的前身Oak（橡树）。

1995年互联网潮流兴起，Oak终于找到适合自己发展的市场，起死回生并蜕变成java。5月23日在SunWorld大会上正式发布了JDK1.0
版本，口号为：“Write once,Run anywhere”,至此java诞生！

1997年Sun发布了JDK1.1，java技术的一些最基础的支撑点（如JDBC，JAR文件格式，JavaBean，RMI等）都是是在java1.1版本
中发布的。此外java语法也有了一些发展，比如加入了内部类（inner class）和反射（reflection）。

1998年JDK迎来了一个类似里程碑的版本JDK1.2，Sun在这个版本中把java技术体系拆分为3个方向，
分别是面向桌面应用开发的J2SE(java 2 platform,standard edition),面向移动端开发的J2ME(java 2 platform,Micro edition)和
面相向企业级开发的J2EE(java 2 platform,enterprise edition).这个版本中出现的代表技术非常多，比如EJB、java plugin-in、
java IDL、swing等。并且在这个版本中java虚拟机第一次内置了JIT（just in time）编译器，JDK1.2曾并存过3个虚拟机，
classic VM、hotspot VM和exact VM，hotspot和exact VM都内置了JIT，而classic VM只能以外挂的形式使用JIT。

2000年Sun发布了JDK1.3。JDK1.3相对于JDK1.2主要的改进表现在一些类库上（如数学运算和新的Timer API等），这个版本对
java 2D做了很多改进，提供了最新的java 2D API，并添加了JavaSound类库。自JDK1.3开始，Sun维持了一个习惯：大约每隔
两年发布一个JDK主版本。

2002年2月13日，JDK1.4发布。JDK1.4是java真正走向成熟的一个版本，哪怕十多年后的今天，仍然后许多主流的应用
（spring、hibernate、struts等）能直接运行在JDK1.4之上。JDK1.4发布了很多新的技术特性，如正则表达式，
异常链、NIO（非阻塞缓存）和日志、XML解析等。

2004年9月30日，JDK1.5发布。从JDK1.2以来，java在语法层面上的变化一直很小，而JDK1.5在java语法易用性上做了非常大的改进。
例如自动封装、范型、动态注解、枚举、可变长参数和遍历循环foreach等。另外JDK1.5是官方申明可以支持windows 9x平台的最后
一个JDK版本。

2006年12月11日，JDK1.6发布。在这个版本中Sun结束了从JDK1.2以来的J2EE、J2SE和J2ME的命名方式，改用Java EE6、Java SE6
和Java ME6的命名方式。JDK1.6的改进包括：提供动态语言支持、提供编译API和微型http服务器API等，同时对java虚拟机做了大量的
改进。在当年的JavaOne大会上Sun公司宣布将java开源，并在随后的一年多时间内陆续将JDK的各个部分在GPL v2的协议下公开了源码，
同时建立了OpenJDK组织对这些源码进行独立管理。

2009年2月19日，JDK1.7完成了其第一个里程碑版本。根据JDK1.7的功能规划，一共设置了10个里程碑，最后一个原计划于2010年9月9日
结束，但是由于各种原因JDK最终无法按计划完成。在JDK1.7对ARM指令集架构提供了支持，至此，JDK可以运行在Windows（不含Windows 9x）、
Linux、Solaris和Mac OS平台上，支持ARM、x86、x64和Sparc指令集。

2009年4月20日。Oracle以74亿美元收购Sun公司，java商标从此归为Oracle所有。

注：本文内容引用了周志明的《深入理解Java虚拟机》。

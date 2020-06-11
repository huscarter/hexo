---
title: flutter简介
date: 2020-01-28 20:18:44
categories: flutter
tags:
     - flutter
description: 介绍了flutter的优缺点。
---

### 简介
flutter 是 Google推出的并源移动应用开发框架，主打跨平台、高保真、高性能。flutter使用Dart开发，一套代码可以同时运行在iOS、Android和web平台。目前跨平台开发的框架还有RN（react native）、weex和Cordova，但是这三个框架都有一个致命的缺点就是运行效率不高，因为他们都是基于js调用原生来实现的跨平台。而flutter是直接使用系统资源。

### flutter的优缺点
1. 跨平台自绘引擎
Flutter既不使用WebView，也不使用操作系统的原生控件。 Flutter使用自己的高性能渲染引擎（skia）来绘制widget（视图），而Skia是跨平台的。目前Flutter目前默认支持iOS、Android、Fuchsia移动平台。

2. 高性能
Flutter采用Dart语言开发,Dart支持 JIT（即时编译）和AOT（运行前编译）编译，在JIT模式下速度与 JavaScript基本持平；当以 AOT模式运行时，速度远快于JavaScript，所以flutter秒杀RN和WEEX。

3. 热度极高
目前开发的主流语言python在github上的star是56k，kotlin是29k，而flutter居然由75k。他的热度是其他语言的好几倍。

4.为公司减少了人力和时间成本。

### flutter的缺点
1. 框架不够成熟和完善
flutter在2018年2月份发布beta版，目前版本是1.9。

2. 社区发展不充分
目前主要flutter社区：flutter官网、flutter中文网、github和stackoverflow。本土社区和第三方库提供不足。

3. 跨平台开发的的问题
iOS和Android用户使用习惯的整合问题。比如iOS左滑删除和Android的长按删除，在跨平台开发中是统一还是区分实现？

### 总结
虽然flutter现在不是十分完善，但是已然成为了跨平台开发的首选。这个是目前公司通过flutter开发的IM项目，有兴趣的可以通过[下载连接](https://m.lianlianlink.com/app/download)下载。
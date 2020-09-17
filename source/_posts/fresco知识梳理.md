---
title: fresco知识梳理
date: 2020-09-12 20:29:44
categories: android
tags:
     - android
description: 梳理了对于我们开发经常使用的fresco的工作原理。
---

对于图片框架的学习最重要就是搞懂2个内容，一个是它的缓存机制，一个是他的生命周期管理。

### fresco 简介
glide有FaceBook提供，[github地址](https://github.com/facebook/fresco)，glide默认使用ARGB_8888加载图片。

1. 优点
    - 功能强大，提供了各种效果的api
    - 提供三级缓存，其中包括了图片解码之后的缓存BitMap，有利于图片的显示效率。
    - 对于android5.0的系统fresco使用ashmem（匿名共享内存）来实现内存缓存，避免频繁GC。
    - 支持gif。
 
2. 缺点
    - 框架体积较大。（2M多）
    - 入侵性强，使用DraweeView显示图片组件。

### 缓存机制（三级缓存）
fresco会先查找BitMap缓存，没有找到就查找Byte[]缓存，还没找到就查找磁盘缓存，如果都没找到就发起网络请求了；之后将下载的图片在3个缓存中保存起来。

1. BitMap缓存（解码图片缓存）
    - fresco会将原始图片解码缓存到ashmem内存。
    - 如果想只加载视图区域大小的图片需要使用ResizeOptions，它会在图片未解码时对图片进行压缩。

2. Byte[]缓存（未解码图片缓存）。
    - 同磁盘读取的图片会先转成Byte[]缓存起来。
    
3. 磁盘缓存

4. 网络获取图片
    - 默认使用HttpURLConnection进行网络请求。
    
### 生命周期管理

参考:
[glide解析](https://blog.csdn.net/sinyu890807/column/info/15318)



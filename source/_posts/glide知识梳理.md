---
title: glide知识梳理
date: 2020-09-12 20:29:44
categories: android
tags:
     - android
description: 梳理了对于我们开发经常使用的glide的工作原理。
---

对于图片框架的学习最重要就是搞懂2个内容，一个是它的缓存机制，一个是他的生命周期管理。

### glide简介
glide有BumpTech提供，[github地址](https://github.com/bumptech/glide)，glide默认使用ARGB_565加载图片。

1. 优点
    - 使用方便Glide.with(activity).load("url").into(ImageView)
    - 图片加载快。（glide会缓存对应ImageView尺寸的图片，所以加载时少了图片压缩环节）
    - 框架体积相对小（400KB左右）。
    - 支持gif。

### 缓存机制（二级缓存）
1. 内存缓存。使用Lru Cache（least recently used）
    - glide加载图片不会将原图加载显示，而是会根据View的尺寸来压缩图片再加载显示，默认缓存的也是压缩之后的图片。
    - EngineKey是glide缓存图片的key，它是由id、width、height等信息组成。
2. 硬盘缓存。
    - 自定义DiskLruCache实现。

3. 网络图片加载
    - glide默认使用HttpUrlConnection进行网络请求。
    
### 生命周期管理
1. Glide.with(Context\View)时，其生命周和整个应用相同。
2. Glide.with(Activity\Fragment)时，生命周期和Activity相同。
通过Activity\Fragment创建一个隐藏的Fragment（RequestManagerFragment），此Fragment内部有一个ActivityFragmentLifecycle暴露给RequestManager；
当Fragment生命周期发生变化时就会调用ActivityFragmentLifecycle，从而实现glide的生命周期管理。

参考:
[glide解析](https://blog.csdn.net/sinyu890807/column/info/15318)



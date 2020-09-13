---
title: OkHttp知识梳理
date: 2020-09-11 20:29:44
categories: android
tags:
     - android
description: 梳理了对于我们开发经常使用的OkHttp的工作原理。
---

OkHttp由Square公司开源，是一款十分优秀的网络请求框架。

## 优点
1. 支持Http2.0（多路复用、头部压缩、服务器主动推送等）
2. 会自动重定向和重连。
3. 支持gzip压缩响应体（gzip是多种压缩软件的简称，压缩算法基于DEFLATE）
4. 通过缓存避免了重复请求。
5. 支持WebSocket。

## 关键类
1. OkHttpClient
利用了外观设计模式，将dispatcher，newCall，interceptor集合在一起。
2. Dispatcher
网络请求调度员，里面有三个ArrayDeque和一个线程池。
    - RunningSyncCalls 存放同步请求。
    - RunningAsyncCalls 存放正在运行的异步请求。
    - ReadyAsyncCalls 当正在请求的异步数量超过64或者同一个host下的请求数超过5则放入这个队列等待。
    - ExecutorService 异步请求线程池，核心数0，最大线程数Integer.MAX_VALUE，包活时间1分钟。
3. RealCall 
4. Interceptor
4.1 RetryAndForwardUpInterceptor
4.2 BridgeInterceptor
4.3 CacheInterceptor
4.4 ConnectInterceptor
4.5 CallServerInterceptor

## 具体请求流程
### 建立请求
### 封装请求
### 缓存访问
### 连接
### 访问服务器
### 取消请求

参考:

![OKHttp源码解析](https://blog.csdn.net/json_it/article/details/78404010)



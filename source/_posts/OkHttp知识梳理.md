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
注意：每一个OkHttpClient都有自己单独的连接池和线程池。

2. Dispatcher
网络请求调度员，里面有三个ArrayDeque和一个线程池。
    - RunningSyncCalls 存放同步请求。
    - RunningAsyncCalls 存放正在运行的异步请求。
    - ReadyAsyncCalls 当正在请求的异步数量超过64或者同一个host下的请求数超过5则放入这个队列等待。
    - ExecutorService 异步请求线程池，核心数0，最大线程数Integer.MAX_VALUE，包活时间1分钟。

3. RealCall（只能请求一次)
真正执行网络请求的类，里面有子类AsyncCall用于异步网络请求。
    - Transmitter 连接OkHttp应用和网络层的桥梁。

4. Interceptor 
4.1 RetryAndForwardUpInterceptor
用于重连和重定向。主要处理判断路由失败和连接异常等情况。

4.2 BridgeInterceptor
主要是为请求（Request）和响应（Response）添加头部信息

4.3 CacheInterceptor
OkHttp的缓存实现。以下根据Http缓存的优先级介绍缓存流程。
4.3.1 强制缓存
cache-control:public, max-age=31536000，public表示缓存对所有用户共享，max-age是过期时间。当public变成no-cache时出发对比缓存。
4.3.2 对比缓存
    - Response返回Etag:"AFY10-6MddXmSerSiXP1ZTiU65VS"，Request发送If-None-Match:"AFY10-6MddXmSerSiXP1ZTiU65VS"
    - Response返回Last-Modified:Sat, 11 Nov 2017 10:30:01 GMT，Request发送If-Modified-Since: Sat, 11 Nov 2017 10:30:01 GMT
4.4 ConnectInterceptor
    - StreamAllocation（流分配）：生成一个RealConnection和HttpCodec。
    - RealConnection：进行连接操作，使用了连接池。
    - HttpCodec：Encodes HTTP requests and decodes HTTP responses。
    - ConnectionPool：连接池，实现了连接的复用。核心线程数0，最大线程数Integer.MAX_VALUE，保活60s。

4.5 CallServerInterceptor
利用HttpCodec完成最终请求的发送。

## 具体请求流程
### 建立请求
### 封装请求
### 缓存访问
### 连接
### 访问服务器
### 取消请求
Request具有tag属性，可以给每个Request当前的activity为标示设置tag，取消时需要取消正在请求的runningCall和在排队的ReadyAsyncCall。

### 请求线程的优先级设置
可以给Request设置priority属性（可以使用子类或者使用自定义header），传给RealCall之后对线程池的队列进行按优先级排序，优先级高的先执行。

参考:
![OKHttp源码解析](https://blog.csdn.net/json_it/article/details/78404010)



---
title: java线程知识梳理
date: 2020-08-15 15:18:44
categories: j2ee
tags:
     - java
description: 多线程是一个重要的部分，现针对实际开发可能遇到的场景做一个知识梳理。
---
java的线程安全绝大多是都涉及JUC（java.util.concurrent）下面的内容，如果是android开发需要再加上Handler、AsyncTask、IntentService、HandlerThread和Android线程池相关内容。

## JUC部分

### 锁（Lock）
#### 对象结构
1、对象头
1.1 Mark Word
    - HashCode
    - 锁标志位
    - GC分代
    - 偏向线程ID
    
1.2 类型指针

2、实例数据

3、对齐补充

#### 乐观锁
认为碰到并发写可能性低，每次拿数据不会上锁；但再更新会比较当前版本号和上一次版本号是否一致，否则重复读取-比较过程。
1. 偏向锁

2. 轻量级锁（自旋锁、自适应自旋锁）
2.1 CAS
2.2 Volatile

#### 悲观锁
遇到并发写的可能性高，每次去拿数据的时候都认为别人会修改。
1. Synchronized

2. ReentrantLock

3. ReadWriteLock

4.  ReentrantReadWriteLock

#### 锁的工具类
1. CycleBarrier

2. CountDownLatch

### Atomic

#### AtomicInteger

#### AtomicLong

#### AtomicBoolean

#### AtomicLongArray

#### AtomicReference

### 线程安全类

### 队列

#### ArrayBlockingQueue

#### DelayQueue

#### LinkedBlockQueue

#### PriorityBlockQueue

### 线程池

## android部分

### 异步实现方式

#### ThreadLocal
1. 通过创ThreadLocal对象存储各个子线程存储各自的内容。使用场景就是android的Looper存储。使用ThreadLocal避免了在主线程创建一个保存各个子线程Looper的map。
2. 底层存储结构，通过Thread作为Key获取到一个 ThreadLocalMap 对象（ThreadLocalMap是静态内部类）,通过这个 ThreadLocalMap存储实际内容
3. 注意问题，内存泄露。最好在不用时手动调一下 ThreadLocal 的 remove方法。

#### Handler

#### AsyncTask

#### ThreadHandle

#### IntentService

### 线程池

#### FixedThreadPool

#### ScheduleThreadPool

#### CacheThreadPool

#### SingleThreadPool




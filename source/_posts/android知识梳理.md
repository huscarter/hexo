---
title: android知识梳理
date: 2018-08-06 20:29:44
categories: android
tags:
     - android
description: 最近在找工作，抽时间重新整理了下android开发所用到的知识，在此整理记录提供自己回顾。
---

# java知识体系

## java运行原理
详情见：[java运行原理](/j2ee/java运行原理)

### java内存结构
1. 程序计数器 
用于存储当前线程所执行字节码的行号指示器。

2. 栈
    - 虚拟机栈（每个方法在执行的同时都会创建一个栈桢用于存储局部变量表、操作数栈、动态链接、方法出口等信息）。
    - 本地方法栈（本地方法栈为虚拟机使用到的native方法服务，有些虚拟机会把本地方法栈合并到虚拟机栈）。
    
3. 堆
是java虚拟机所管理内存中最大的一块，他是线程共享的，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，也是GC垃圾收集管理的主要区域（也被称为GC heap）。

4. 方法区
方法区是线程共享的内存区域，他被用于存储已被虚拟机加载的类信息、常量、静态变量和即时编译后的代码等数据。

### java文件结构
1. 魔数和class版本
2. 常量池
3. 访问标志
4. 类索引、父类索引和接口索引集合
5. 字段表集合
6. 方法表集合
7. 属性表集合

### java文件的加载过程
1. 加载 
    - 双亲委派机制:为了生成对象的可识别（由同一ClassLoader加载），类首先交由父类加载器去加载。
2. 验证
    - 验证.class文件的正确性，比如魔术、版本、访问标志等。（一般都是正确的，但是如果.class文件是自己通过其他方式生成就有可能出现问题）
3. 准备
    - 为静态变量分配内存（并不是为对象，此时还没有生成对象）
4. 初始化
    - 执行类的构造方法，为对象分配内存和初始值。
5. 卸载
    - 

### 虚拟机
#### 虚拟机分类
1. Sun Class/Exact VM（外挂JIT）

2. HotSpot VM （JDK 1.3正式加入）

3. google android Dalvik VM（不是java虚拟机，因为不符合java虚拟机规范）

#### 虚拟机内对象分析
1. 对象的创建
    - 检测类是否被加载过否则先加载类
    - 分配内存空间（对具有内存整理的收集器采用指针碰撞，否则采用闲散列表）
    - 初始化为零值
2. 对象的内存布局
    - 对象头（用于存储对象自身的运行时数据，如哈希码、GC分代年龄、锁状态标志、偏向线程ID、偏向时间戳和类型指针等）
    - 实例数据（真正存储的有效信息，比如各种类型的字段内容，包括从父类继承过来的）
3. 对象的访问定位
    - 句柄访问

#### 垃圾收集器
1. 对象可回收算法
    - 标记清除算法（对象被引用标记+1引用失效标记-1当标记为0时对象可被回收，不能很好解决对象相互引用的问题）
    - 可达性分析算法（通过GC Root的节点向下搜索，搜索路径为引用连，当对象到达GC Root没有任何引用链时对象可被回收；激活的线程、栈中的对象和静态类可做GC Root）

2. 垃圾收集算法
    - 标记-清除算法（标记可回收的对象，一次性清除；缺点造成内存碎片）
    - 复制算法（将内存氛围eden和survivor，GC时将存活的对象复制到survivor，一般应用在新生代）
    - 标记-整理算法（标记可回收对象，将存活的对象移动到内存的一边，一般应用在永久代）
    - 分代收集算法（新生代使用复制算法，永久代使用标记-整理算法）

### java线程

#### 线程的状态
1. new
    - new Thread()
    
2. runnable
    - 调用start()后线程等待CPU时间片分配
    
3. blocked
    - 等待阻塞 调用wait()让线程等待某项工作完成
    - 同步阻塞 线程获取synchronized同步锁失败
    - 其他阻塞 调用sleep（直接睡眠不释放资源）和join（内部调用wait清醒等待会释放资源）方法
    (join方法解释:thread parent 的run方法中调用thread child的join方法，此时thread parent会等待thread child结束后再执行)
4. running 正在运行
5. terminate 执行完程序或者异常退出

#### java的死锁
1. 产生的原因
    两个线程相互竞争对方的资源，造成彼此无法继续运行的现象。
    
2. 解决方案
    - 调整锁的范围
    - 调整锁的顺序

## android知识体系

### android体系结构简介

### android四大组建工作原理
#### activity
1. 生命周期
onCreate-->onStart-->onResume-->running-->onPause-->onStop-->onDestroy

2. launch model
    - standard
    - singleTop
    - singleTask
    - instance

#### service
1. 生命周期
    - startService-->onCreate-->onStartCommand-->running-->onDestroy
    - bindService-->onCreate-->onBind-->binding-->onUnBind-->onDestroy

2. 启动方式
    - startService
    - bindService

#### broadcast receiver
略

#### content provider
略

#### Android进程的优先级
1. 前台进程
    - activity（正在和用户交互的）
    - service（正在运行或者是被标记为前台）
    - broadcast receiver（正在执行onReceive方法）
    
2. 可见不可交互
    - activity（被遮挡部分的activity）
    
3. 后台进程
    - activity（不可见）
    - service（等待bind）
    - broadcast receiver（等待接受消息）
    
4. 空进程
    - Android经常会将走完生命周期的组件保存在内存以待之后启动使用，用来提高启动速度。

### View

#### View的事件分发机制
事件分发机制类似上级有个问题，一级级下达下去，有最底层的人员处理解决掉。如果最底层的人员不能解决掉，那么就一层层往上抛。
事件是由activity -> window -> decor  -> view -> 子view一级级往下分发，通过dispatchTouchEvent(MotionEvent ev),
onInterceptTouchEvent(MotionEvent ev)和onTouchEvent(MotionEvent ev)三个方法来实现。

#### View的滑动冲突
通过外层的onInterceptTouchEvent事件拦截和内部的onTouchEvent事件里调用requestDisallowInterceptTouchEvent拦截事件实现。

### android 的IPC

### android的消息机制

### android的图片处理

### android的组件篇
#### RecyclerView
#### NestedScrollView

### android开发模式
#### MVC
#### MPV
#### MVVM

### android的架构

### 第三方框架
#### fresco
#### glide
#### okhttp
#### rxjava
#### retrofit

## 网络通信相关

### TCP

### UDP

### HTTP和HTTPS



## 数据库相关

### SQLite

### greenDAO





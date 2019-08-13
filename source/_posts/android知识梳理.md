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
1. 魔数（0xCAFEBABE）和class版本(从45开始)
2. 常量池
3. 访问标志
4. 类索引、父类索引和接口索引集合
5. 字段表集合
6. 方法表集合
7. 属性表集合

### java文件的加载过程
1. 加载 
    - 双亲委派机制:为了生成对象可识别（由同一ClassLoader加载），类首先交由父类加载器加载。
    - BootstrapClassLoader,ExtensionClassLoader,ApplicationCLassLoader
2. 验证
    - 验证.class文件的正确性，比如魔术、版本、访问标志等。（一般都是正确的，但是如果.class文件是自己通过其他方式生成就有可能出现问题）
3. 准备
    - 为静态变量分配内存（并不是为对象，此时还没有生成对象）
5. 解析
4. 初始化
    - 执行类的构造方法，为对象分配内存和初始值。
5. 卸载

### 虚拟机
详情见：[java虚拟机](/j2ee/java虚拟机)

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
    - 直接访问

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
onCreate-->onStart(可见)-->onResume(可操作)-->running-->onPause(不可操作、内存不足可能回收)-->onStop(不可见、必要时可能回收)-->onDestroy

2. launch model
    - standard
    - singleTop
    - singleTask
    - singleInstance

#### service
1. 生命周期
    - startService-->onCreate-->onStartCommand-->running-->onDestroy
    - bindService-->onCreate-->onBind-->binding-->onUnBind-->onDestroy

2. 启动方式
    - startService
    - bindService

#### broadcast receiver
1. 广播的注册
    - 静态注册（AndroidManifest），不需要取消注册
    - java代码动态注册，需要自己取消注册
    
2. 发送
```
Intent intent = new Intent();
intent.setAction("com.ryg.receiver.LAUNCH");
sendBroadcast(intent);
```

3. 接收
```
@Override
public void onReceive(Context context,Intent intent) {
 // onReceive函数不能做耗时的事情，参考值：10s以内
 Log.d("scott","on receive action=" + intent.getAction());
 String action = intent.getAction();
 // do some works
}
```

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
事件是由activity -> window -> decor  -> view -> 子view一级级往下分发，通过dispatchTouchEvent(MotionEvent ev),onInterceptTouchEvent(MotionEvent ev)和onTouchEvent(MotionEvent ev)三个方法来实现。
如下面的为代码：
```
public boolean dispatchTouchEvent(MotionEvent ev) { 
    boolean consume = false;
    if(onInterceptTouchEvent(ev)) { 
        consume = onTouchEvent(ev);
    } else { 
        consume = child.dispatchTouchEvent(ev); 
    }
    return consume; 
}
```

#### View的滑动冲突
通过外层的onInterceptTouchEvent事件拦截和内部的onTouchEvent事件里调用 requestDisallowInterceptTouchEvent 拦截事件实现。

### android 的IPC
略

### android的消息机制
Handle,MessageQueue和Looper共同协作的过程。

1. MessageQueue消息队列用于存储、查询和删除handle发送的消息，MessageQueue是单链表结构，所以它的插入和删除比较快。
MessageQueue提供了enqueueMessage和next方法来插入和查询消息，next方法是一个无限循环的方法"for(;;)"，用于读取消息并删除以读取的消息，
如果没有消息，会执行nativePollOnce(ptr,-1)进入等待状态。

2. Looper通过ThreadLocal实现，作用是创建和使用MessageQueue。Looper的构造方法创建了MessageQueue，Looper的loo()方法进行了消息循环。

3. Handler是消息的发送和接收处理者。消息发送分两种一种是post(Runnable)，一种是send(Message);其实post最终也是调用send(Message)，
Runnable被赋值给了Message的callback属性。最后Looper循环到了消息会调用handle的dispatchMessage方法将逻辑处理切回到handle线程。


### android的图片处理
Android对每一个app分配的内存使用大小有限，而图片加载十分消耗内存，所以图片处理在Android中是一个重点。

1. Bitmap的加载：decodeFile,decodeResource,decodeStream和decodeByteArray。

2. 采用BitmapFactory.option来对图片进行处理，一般是通过inSampleSize实现取样处理。

3. 通过LruCache和DiskLruCache实现内存和本地缓存。LRU（最近最少使用算法）是通过期LinkedHashMap数据结构实现的，LinkedHashMap是有序的Map，其顺序
有两种（插入顺序和读取顺序）；LRU采用读取顺序，如果数据被读取了一次，该数据会被放置链表的末尾。

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
1. 缓存策略
分三部分：Bitmap缓存，未解码的图片存内存，磁盘缓存。

#### glide
1. 缓存策略
分三部分：内存缓存使用LruCache，activeResource(软引用缓存)，硬盘缓存

#### picasso
1. 缓存策略
分二部分：内存缓存使用LruCache，硬盘缓存

#### okhttp

#### rxjava

#### retrofit

## 网络通信相关

### OSI(open system interconnection)分层
1. 物理层 传输比特流，媒介为网线、网卡
2. 数据链路层 传输帧数据，媒介交换机、网桥
3. 网络层 路由传输组包后的数据，媒介路由器，协议 IP
4. 传输层 传输包数据，协议TCP、UDP
5. 会话层 传输包数据，会话(session)管理，设计单工和双工通信
6. 表示层 数据经过编码或者解码
7. 应用层 ftp、http、telnet和pop3等协议传输和使用数据

### TCP(transmission control protocol)
1. 三次握手
    - client:SYN=x
    - server:ACK=SYN+1;SYN=y
    - client:ACK=y+1
    
2. 四次挥手
    - client:FIN=x
    - server:ACK=x+1
    - server:FIN=Y
    - client:ACK=Y+1 
    -->server close --client close

### UDP(user datagram protocol)

### HTTP(HyperText transfer protocol)
1. 版本差异
    - 1.0
    问题：tcp连接不能复用（一个页面由多个小图片，每个图片也要重新尽心握手连接）和排头堵塞（head of line blocking，请求是先进先出导致后续请求必须等待前面的完成才能开始）
    - 1.1
    优点：支持持久连接（在一个tcp连接上可以发送多个http请求和响应），建立了请求头（header）来支持持久连接和创建虚拟站点（ip和端口好相同的不同站点），增加了请求方法（1.0只有三种GET,POST和HEAD）和状态代码
    问题：请求可以不用等待上一个响应结束就发起，但是服务器返回仍然要按照请求顺序返回，以确保客户端接受数据的准确性，没有完全解决HOL问题。
    - 2.0 
    优点：分帧发送、多路复用，请求和响应不用在按照发送的顺序，大大提高了性能。采用了首部压缩。实现了服务端推送。
    
2. 请求和响应

### HTTPS
1. 请求过程
    - client：向服务器请求连接【服务器你好，我想建立连接】
    - server：服务器回应请求，并发送自己的数字证书【客户端你好，我是服务器；这是我的数字证书（数字证书包含公钥）】
    - client：请证明你是真的服务器【客户端用公钥生成一个字符串发给服务器】
    - server：服务器用私钥解密客户端的字符串了解了是要证明自己的真实身份，之后服务器回复客户端【服务器返回一个用私钥加密的字符串】
    - client：客户端用公钥解密服务器的字符串确认了服务器的身份，客户端发送了对称加密信息【对称加密算法和密钥】
    - server：服务器收到对称加密信息，之后使用对称加密通信
    
2. SSL/TSL(secure socket layer / transport layer security)
SSL安全套接层是早起为了解决http明文传输问题而产生的，当SSL更新到3.0版本时IETF（The Internet Engineering Task Force 互联网工程任务组）将其更名为TLS。

### 加密技术
1. 对称加密
加密和解密数据都是用同一个密钥。常被用作信心交换。
常用的算法由DES(data encryption standard)、RC5和AES(Advanced Encryption Standard)

2. 非对称加密
公钥对外公开，使用公钥加密的数据只能由私钥解密获得原文。常被用作网络握手连接。
常用的算法由RSA、ECC和DSA

3. hash算法
是一种单项的算法，对过hash算法对目标产生一个长度固定的hash值，常被用作数字签名。
MD5(message digest algorithm)和SHA

## 数据库相关

### SQLite

### greenDAO

### 算法
1. 排序算法

2. 其他算法







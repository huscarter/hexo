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
2.1 虚拟机栈（每个方法在执行的同时都会创建一个栈桢用于存储局部变量表、操作数栈、动态链接、方法出口等信息）。
2.2 本地方法栈（本地方法栈为虚拟机使用到的native方法服务，有些虚拟机会把本地方法栈合并到虚拟机栈）。
    
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
1.1 双亲委派机制:为了生成对象可识别（由同一ClassLoader加载），类首先交由父类加载器加载。
1.2 java自带的三大加载器：BootstrapClassLoader > ExtensionClassLoader > ApplicationCLassLoader

2. 验证
2.1 验证.class文件的正确性，比如魔术、版本、访问标志等。（一般都是正确的，但是如果.class文件是自己通过其他方式生成就有可能出现问题）

3. 准备
3.1 为静态变量分配内存（并不是为对象，此时还没有生成对象）

4. 解析
4.1 将符号引用转成直接引用

5. 初始化
5.1 执行类的构造方法，为对象分配内存和初始值。

6. 卸载

### 虚拟机
详情见：[java虚拟机](/j2ee/java虚拟机)

#### 虚拟机分类
1. Sun Class/Exact VM（外挂JIT）

2. HotSpot VM （JDK 1.3正式加入）

3. google android Dalvik VM（不是java虚拟机，因为不符合java虚拟机规范）

#### 虚拟机内对象分析
1. 对象的创建
1.1 检测类是否被加载过否则先加载类
1.2 分配内存空间（对具有内存整理的收集器采用指针碰撞，否则采用闲散列表）
1.3 初始化为零值
    
2. 对象的内存布局
2.1 对象头（用于存储对象自身的运行时数据，如哈希码、GC分代年龄、锁状态标志、偏向线程ID、偏向时间戳和类型指针等）
2.2 实例数据（真正存储的有效信息，比如各种类型的字段内容，包括从父类继承过来的）
    
3. 对象的访问定位
3.1 句柄访问
3.2 直接访问

#### 垃圾收集器
1. 对象可回收算法
1.1 标记清除算法（对象被引用标记+1引用失效标记-1当标记为0时对象可被回收，不能很好解决对象相互引用的问题）
1.2 可达性分析算法（通过GC Root的节点向下搜索，搜索路径为引用连，当对象到达GC Root没有任何引用链时对象可被回收；激活的线程、栈中的对象和静态类可做GC Root）

2. 垃圾收集算法
2.1 标记-清除算法（标记可回收的对象，一次性清除；缺点造成内存碎片）
2.2 复制算法（将内存氛围eden和survivor，GC时将存活的对象复制到survivor，一般应用在新生代）
2.3 标记-整理算法（标记可回收对象，将存活的对象移动到内存的一边，一般应用在永久代）
2.4 分代收集算法（新生代使用复制算法，永久代使用标记-整理算法）

### java线程

#### 线程的状态
1. new
1.1 new Thread()
 
2. runnable
2.1 调用start()后线程等待CPU时间片分配
    
3. blocked
3.1 等待阻塞 调用wait()让线程等待某项工作完成
3.2 同步阻塞 线程获取synchronized同步锁失败
3.3 其他阻塞 
3.3.1 调用sleep 直接睡眠不释放资源。
3.3.2 调用join（内部调用wait清醒等待会释放资源）方法。(join方法解释:thread parent 的run方法中调用thread child的join方法，此时thread parent会等待thread child结束后再执行)
3.3.3 调用yield 阻塞当前线程，当前线程的资源不会释放，但是让出下一次的cpu轮转让其他线程去执行任务。
3.3.4 调用wait 当前线程释放资源等待。
    
4. running 正在运行
5. terminate 执行完程序或者异常退出

#### java锁（可见行、原子性和有序性）
1. synchronized (能确保同一时刻只执行某个代码块或者方法，也可以确保线程的可见行)
1.1 普通同步方法（实例方法）：锁是当前实例对象，进入同步代码前要获得当前实例的锁
1.2 静态同步方法：锁是当前类的class对象，进入同步代码前要获得当前class对象的锁
1.3 同步方法块：锁是括号里面的对象，对给定对象加锁，进入同步代码块前要获得给定对象的锁。 

2. ReentrantLock（手动可重入锁）
2.1 创建一个ReentrantLock对象在线程中手动调用（提供lock和unlock方法）
2.2 如果构造方法传入true，则是公平锁，会根据等待的时长来调度资源
2.3 可以响应中断（interrupt）线程被中断后资源释放给其他线程
2.4 可以设置限时等待通过ReentrantLock.tryLock(long timeout,TimeUnit unit)，指定时间内没有获得锁就返回false，告诉业务逻辑去做其他事。

3. ReentrantReadWriteLock
3.1 一共一个读取锁和一个写入锁，读取锁是共享的，写入锁是互斥的。

4. volatile（确保对象的可见性，有序性）

#### java的死锁
1. 产生的原因
两个线程相互竞争对方的资源，造成彼此无法继续运行的现象。
    
2. 解决方案
2.1 调整锁的范围
2.3 调整锁的顺序

## android知识体系

### android体系结构简介
1. application

2. framework
2.1 ActivityManager
2.2 WindowManager
2.3 ServiceManager
2.4 PackageManager
2.5 TelephoneManager
2.6 LocationManager
2.7 ContentProvider
2.8 NotificationManager
2.9 ...

3. library & runtime
3.1 SurfaceManager
3.2 OpenGL
3.3 SSL
3.4 WebKit
3.5 SQLite
3.6 ...

4. linux kernel
4.1 Display Driver
4.2 Camera Driver
4.3 Bluetooth Driver
4.4 USB Driver
4.5 IBinder Driver
4.6 WIFI Driver
4.7 Power Driver
4.8 ...
 
### Context（abstract class Context）
官方定义：提供了关于应用程序全局信息的接口。可以用来开启Activity、Service发送广播、获取资源和数据库等；可以理解context为剧本，四大组件为主演。
1. Activity extends ContextThemeWrapper
2. Service extends ContextWrapper
3. Application extends ContextWrapper
4. BroadcastReceiver 和 ContentProvider不是Context
5. getApplication 和 getApplicationContext其实地址指向相同，只不过getApplication只有Activity和Service提供接口
 
### [android的启动过程](android/android的启动)

### activity的加载过程

### android四大组建工作原理
#### activity
1. 生命周期
1.1 普通：onCreate-->onStart(可见)-->onResume(可操作)-->running-->onPause(不可操作、内存不足可能回收)-->onStop(不可见、必要时可能回收)-->onDestroy
1.2 页面被覆盖重新展示：onPause --> onStop -->被覆盖-->重新展示-->onRestart-->onStart-->onResume // 注意：只有旧的页面onPause之后新的页面才会走onCreate
1.3 配置发生修改：onPause --> onSaveInstanceState(一定是在onStop之前) --> onStop --> onDestroy-->onCreate-->-->onStart-->onRestoreInstanceState（一定是在onStart之后）-->onResume 
1.4 可通过android:configChanges="orientation"来设置是否发生修改
1.5 按home键：onPause --> onSaveInstanceState(一定是在onStop之前) --> onStop --> home -->onSaveInstanceState --> onRestart --> onStart --> onResume
 
2. launch model
2.1 standard 每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在；谁启动了这个Activity，那么这个Activity就运行在启动它的那个Activity所在的栈中。
2.2 singleTop 如果新Activity已经位于任务栈的栈顶那么此Activity不会被重新创建，同时它的onNewIntent方法会被回调。如果新Activity的实例已存在但不是位于栈顶，那么新Activity仍然会重新重建。
2.3 singleTask 只要Activity在一个栈中存在，那么多次启动此Activity都不会重新创建实例，系统会回调其onNewIntent；同时singleTask默认具有clearTop的效果。
2.4 singleInstance 除了具有singleTask模式的所有特性外，具有此种模式的Activity只能单独地位于一个任务栈中。
 
3. TaskAffinity （任务相关属性）
3.1 TaskAffinity用于设置任务栈属性，注意不能和包名相同否则等于没有设置
3.2 只能和SingleTask 和 allowTaskReparenting（允许任务重新装修）配合使用，否则无效。

4. allowTaskReparenting
4.1 a应用启动了b应用的activityC，回到桌面点击b应用图标，此时会回到activityC。

#### service
1. 生命周期
1.1 startService-->onCreate（多次 startService 只会调用一次）-->onStartCommand（startService次数一样）-->running-->onDestroy（只会走一次）
1.2 bindService-->onCreate（多次 bindService 只会调用一次）-->onBind（多次 bindService 只会调用一次）-->binding-->onUnBind（只会走一次）-->onDestroy（只会走一次）

2. 启动方式
2.1 startService
2.1.1 必须手动调用stopService或者通过service的stopSelf停止，否则会一直运行。

2.2 bindService
2.2.1 可以通过unbindService停止，当activity关闭时也会自动停止。

3. startService & bindService || bindService & startService（如果一个service被混合了启动和绑定状态）
3.1 先调用stopService不会直接走 onDestroy，而是需要再调用 unBindService 才会执行unBind和onDestroy
3.2 如果先调用unBindService则走unBind回调，再调用stopService才会走onDestroy回调。
3.2 关闭activity效果等同unBind，后面必须再调用stop才能停止service。

#### broadcast receiver
1. 广播的注册
1.1 静态注册（AndroidManifest），不需要取消注册
1.2 java代码动态注册，需要自己取消注册
    
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

#### fragment
1. fragment的作用
1.1 起初为了适配平板宽屏设备，因其特性可以装载在activity实现tab切换。

2. fragment的使用方式
2.1. 静态
2.2. 动态

3. fragment的生命周期

#### SharedPreference
1. 获取的是SharedPreference单利加载入内存，所以不要存储大量数据。
2. SharedPreference的get和put方法都加了同步锁，单个进程是线程安全的。
3. SharedPreference每次edit都会生成EditorImpl对象，推荐多次操作一次提交。
4. SharedPreference在写入磁盘时会生成.back备份文件，首次获取sp对象时发现备份文件会见备份文件重新命名当作正式文件，在多进程中可能会造成源文件信息丢失。

#### Android进程的优先级
1. 前台进程
1.1 activity（正在和用户交互的）
1.2 service（正在运行或者是被标记为前台）
1.3 broadcast receiver（正在执行onReceive方法）
    
2. 可见不可交互
2.1 activity（被遮挡部分的activity）
    
3. 后台进程
3.1 activity（不可见）
3.2 service（等待bind）
3.3 broadcast receiver（等待接受消息）
    
4. 空进程
4.1 Android经常会将走完生命周期的组件保存在内存以待之后启动使用，用来提高启动速度。

### android线程池

#### ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory)
1. corePoolSize：核心线程数（一般cpu数+1）；默认情况下核心线程会在线程池中一直存活。
2. maximumPoolSize：线程池所能容纳的最大线程数（一般cpu数*2+1）；当活动线程数达到这个数值后，后续的新任务将会被阻塞。
3. keepAliveTime：非核心线程闲置时的超时时长（一般1s）；超过这个时长非核心线程就会被回收，当allowCoreThreadTimeOut设置为true，此时长同样作用于核心线程。
4. workQueue：工作队列（一般128）；超过最大线程数时新任务存放于此。如果爆满会抛出RejectedExecutionException。

#### ThreadPool分类
1. FixedThreadPool（会一直有线程）
1.1 只有固定的核心线程数且不会销毁，除非线程池关闭；任务队列大小无限制。
1.2 用于快速响应外界任务。

2. CacheThreadPool（有可能闲暇时无任何线程）
2.1 只有非核心线程数，大小为Integer.MAX_VALUE，闲置60s后线程会被销毁；任务队列SynchronousQueue不存储任何任务，来任务直接用线程执行。
2.2 用于处理大量短时间的任务。
 
3. ScheduledThreadPool（接近ThreadPoolExecutor）
3.1 核心线程数固定；非核心线程数没有限制，但是一旦闲置就会被回收。
3.2 用于处理周期性重复执行的任务
 
4. SingleThreadPool（只有一个核心线程）
4.1 核心线程数1个；无非核心线程；有任务队列。
4.2 略

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

#### view的动画
1. View动画（补间动画），系统提供基础抽象类类Animation
1.1 种类
1.1.1 TranslateAnimation（平移动画）
1.1.2 ScaleAnimation（缩放动画）
1.1.3 RotateAnimation（旋转动画）
1.1.4 AlphaAnimation（透明动画）

1.2 用途
1.2.1 activity切换效果
1.2.2 ViewGroup的出场效果
1.2.3 列表item的出场效果（可以通过LayoutAnimation简单实现哦）

2. 帧动画，系统提供基类AnimationDrawable
播放一张张图片，也是也是view动画，只不过和平移、缩放之类的不同而已。一般用在类似gif的效果实现。

3. 属性动画（api11加入），提供基类ValueAnimation、ObjectAnimation和AnimationSet
只要该对象有此属性就可以动画，功能强大。老版本通过NineOldAndroid间接实现此效果，但是本质依然为view动画。
3.1 时间插值器（TimeInterpolator）按照时间流逝百分比计算属性值改变百分比。
3.2 类型估值器（TypeEvaluator）按照属性变化百分比计算变化后的属性值。
3.3 属性动画的原理，其实是通过反射调用对象属性的set方法在渲染之前修改属性值实现的。如果动画没有给定初始值，对象属性需要提供get方法。

4. 动画需要注意事项
4.1 OOM容易出现在帧动画，尽量避免使用大图，帧动画少用。
4.2 内存泄露，属性动画中有无限循环的动画，activity关闭前需要停止，否则activity不会被销毁。
4.3 view动画状态问题，view动画没有真正改变view的状态，动画之后之后setVisibility隐藏不了，需要调用view.clearAnimation
4.4 view动画位置问题，view动画视觉上位置发生了变化，但是实际相对父空间的left、top等值没有变动，触摸事件仍然是原来的位置，需要手动设置layout以更新位置。（属性动画没有此问题）
 
### android 的IPC
略

### android的handler机制
Handle,MessageQueue和Looper共同协作的过程。

1. MessageQueue消息队列用于存储、查询和删除handle发送的消息，MessageQueue是单链表结构，所以它的插入和删除比较快。
MessageQueue提供了enqueueMessage和next方法来插入和查询消息，next方法是一个无限循环的方法"for(;;)"，用于读取消息并删除以读取的消息，
如果没有消息，会执行nativePollOnce(ptr,-1)进入等待状态。

2. Looper通过ThreadLocal实现，作用是创建和使用MessageQueue。Looper的构造方法创建了MessageQueue，Looper的loo()方法进行了消息循环。

3. Handler是消息的发送和接收处理者。消息发送分两种一种是post(Runnable)，一种是send(Message);其实post最终也是调用send(Message)，
Runnable被赋值给了Message的callback属性。最后Looper循环到了消息会调用handle的dispatchMessage方法将逻辑处理切回到handle线程。

4. 屏障消息

5. 异步消息

### android的图片处理
Android对每一个app分配的内存使用大小有限，而图片加载十分消耗内存，所以图片处理在Android中是一个重点。

1. Bitmap的加载：decodeFile,decodeResource,decodeStream和decodeByteArray。

2. 采用BitmapFactory.option来对图片进行处理，一般是通过inSampleSize实现取样处理。

3. 通过LruCache和DiskLruCache实现内存和本地缓存。LRU（最近最少使用算法）是通过期LinkedHashMap数据结构实现的，LinkedHashMap是有序的Map，其顺序
有两种（插入顺序和读取顺序）；LRU采用读取顺序，如果数据被读取了一次，该数据会被放置链表的末尾。

### android的组件篇
#### RecyclerView
[参考博文](https://zhuanlan.zhihu.com/p/80475040)
1. LayoutManager（用于控制item的排放，横、纵和叠加）
2. RecyclerView.Adapter（规范ViewHolder和缓存机制）
3. ItemAnimator（可以根据ViewHolder在RecyclerView的各个状态添加动画）
4. ItemDecoration（添加item的分割线）
5. 缓存机制
```
public final class Recycler {
    // Scrap (view)：在布局期间进入临时分离状态的子视图。
    
    // 存放ViewHolder对象的ArrayList,这一级缓存是没有容量限制的，只要符合条件的我来者不拒
    final ArrayList<ViewHolder> mAttachedScrap = new ArrayList<>();
    // 存放的是发生了变化的ViewHolder
    ArrayList<ViewHolder> mChangedScrap = null;
    // 存放的是dettach掉的视图
    final ArrayList<ViewHolder> mCachedViews = new ArrayList<ViewHolder>();
    // 保存的ViewHolder不仅仅是removed掉的视图，而且是恢复了出厂设置的视图
    RecycledViewPool mRecyclerPool;
    // 这一级缓存是留给开发者自由发挥的
        private ViewCacheExtension mViewCacheExtension;
    ...
}
```
#### NestedScrollView

### android开发模式
#### MVC
#### MPV
#### MVVM

### android的架构

### 组件化
#### 组件提高编译效率
1. 通过配置gradle将各个组件以单独的application存在
2. 通过发布成maven库，将各个组件以编译好的aar包出现
 
#### 组件化路由和交互
1. ARoute实现路由，原理是利用注解表示Activity的路由，由apt生成路由表最终还是通过startActivity(Intent)实现页面跳转。
2. 组件之间的交互，除了ARoute提供的数据传递之外还可以使用事务总站的方式，比如EventBus和RxBus。
3. ARoute的IOC和AOP
3.1 IOC
3.2 AOP
 
### 插件化
#### 类加载（Android提供2个classLoader加载二进制文件）
1. PathClassLoader extends BaseDexClassLoader
1.1 主要用来加载系统类和应用程序的类
1.2 Dalvik 虚拟机中不能加载未安装的dex，但在Art虚拟机中可以

2. DexClassLoader extends BaseDexClassLoader
2.1 用来加载未安装apk的dex

#### 资源加载
1. android通过将资源路径放入到 AssetManager 中，再将 AssetManager 传给 Resource 对象，就可以通过 Resource 来加载资源。只不过AssetManager构造被hide，所以需要通过反射创建。

#### 组件生命周期管理
1. 通过Hook响应的系统对象实现。

#### 混淆后对应类的匹配
通过Mapping文件识别

### 第三方框架
#### fresco
1. 缓存策略
1.1 Bitmap缓存
1.2 未解码的图片存内存
1.3 磁盘缓存

#### glide
1. 缓存策略
1.1 内存缓存使用LruCache
1.2 activeResource(软引用缓存)
1.3 磁盘缓存

#### picasso
1. 缓存策略
1.1 内存缓存使用LruCache
1.2 硬盘缓存

#### OkHttp
[详细介绍文章](https://www.jianshu.com/p/d98be38a6d3f)

#### RxJava
1. 原理

2. 操作符
2.1 observable.just(1,2 3)// 最多9个
2.2 Observable.fromArray(arr)// 传递的参数是一个数组，解决了just操作符的个数限制的缺点
 
#### retrofit
1. 原理

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
1.1 client:SYN=x
1.2 server:ACK=SYN+1;SYN=y
1.3 client:ACK=y+1
    
2. 四次挥手
2.1 client:FIN=x
2.2 server:ACK=x+1
2.3 server:FIN=Y
2.4 client:ACK=Y+1 
2.5 server close&client close

### UDP(user datagram protocol)

### HTTP(HyperText transfer protocol)
1. 版本差异
1.1 1.0版本
 问题：tcp连接不能复用（一个页面由多个小图片，每个图片也要重新尽心握手连接）和排头堵塞（head of line blocking，请求是先进先出导致后续请求必须等待前面的完成才能开始）
1.2 1.1版本
 优点：支持持久连接（在一个tcp连接上可以发送多个http请求和响应），建立了请求头（header）来支持持久连接和创建虚拟站点（ip和端口好相同的不同站点），增加了请求方法（1.0只有三种GET,POST和HEAD）和状态代码
 问题：请求可以不用等待上一个响应结束就发起，但是服务器返回仍然要按照请求顺序返回，以确保客户端接受数据的准确性，没有完全解决HOL问题。
1.3 2.0版本 
 优点：分帧发送、多路复用，请求和响应不用在按照发送的顺序，大大提高了性能。采用了首部压缩。实现了服务端推送。
    
2. 请求和响应
2.1 请求报文
2.1.1 请求行【请求方法，请求URL（不包括域名），HTTP协议版本】（POST /index http/1.1）
2.1.2 请求头【请求头部由关键字/值对组成，每行一对】（Cache-Control:no-cache）
2.1.3 空白行【通知服务器以下不再有请求头】
2.1.4 请求体【GET没有请求数据，POST有】
2.2 响应报文
2.2.1 状态行【服务器HTTP协议版本，响应状态码，状态码的文本描述】（http/1.1 200 OK）
2.2.2 响应头
2.2.3 空白行
2.2.4 响应体

### HTTPS
#### 单项认证（只有服务器有证书，一般的网络请求）
1. 请求过程
1.1 client：向服务器请求连接【服务器你好，我想建立连接】
1.2 server：服务器回应请求，并发送自己的数字证书【客户端你好，我是服务器；这是我的数字证书（数字证书包含公钥）】
1.3 client：请证明你是真的服务器【客户端用公钥生成一个字符串发给服务器】
1.4 server：服务器用私钥解密客户端的字符串了解了是要证明自己的真实身份，之后服务器回复客户端【服务器返回一个用私钥加密的字符串】
1.5 client：客户端用公钥解密服务器的字符串确认了服务器的身份，客户端发送了对称加密信息【对称加密算法和密钥】
1.6 server：服务器收到对称加密信息，之后使用对称加密通信

2. https安全问题
2.1 加密是在传输层，所以第三方拦截的是密文
2.2 在客户端和服务器接受数据其实是解密之后的明文，为了防止客服端app被劫持拦截信息（主要是发给服务器的信息），客户端可以请求获取服务的公钥加密传输数据。 

#### 双向认证（服务器和客户端都有证书，银联之类的网络请求）
1. 请求过程
略
    
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

### 数据库加密
#### 对储存的数据加密
1. 使用对称加密，有加密和解密过程。

#### 对数据库文件加密
1. SQLCipher（一款开源的数据库加密工具）

### JetPack相关

### [设计模式](设计模式/设计模式整理)

### [数据结构](java/常用数据结构)

### 算法
1. 排序算法

2. 其他算法

### 虚拟机
1. Dalvik
1.1 JIT方式运行，每次运行app都需要将字节码转换成机器码
1.2 优点：安装快，安装后体积小。缺点：启动较慢、运行效率低，更耗电。
    
2. Art
2.1 AOT方式运行，在安装的时候就会吧字节码转换成机器码
2.3 优点：app启动快，运行效率高，省电。缺点：安装慢，安装后体积相比Dalvik大。




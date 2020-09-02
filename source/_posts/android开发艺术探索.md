---
title: android开发艺术探索
date: 2018-07-12 12:29:44
categories: android
tags:
     - android
description: 《android开发艺术探索》是一本针对有一定android基础的同学所写的书，可以帮助读者进一步提高其对android开发的认识。
---

## 一、activity

### activity的生命周期
1. 典型情况
onCreate -> (onRestart ->)onStart(界面可见不可操作) -> onResume(可操作) -> running -> onPause -> onStop(不可见,当采用透明主题时不调用) -> onDestroy

2. 异常情况
2.1 系统配置发生变化（屏幕旋转）
可以配置android:configChanges="orientation|screenSize"来避免重走生命周期，screenSize在api13之后屏幕旋转会导致activity重启。
2.1  内存不足（后台activity被杀死）
如果一个进程中没有四大组件，该进程将很快会被系统杀死，所以一些耗时操作最好时放在service中进行。

### activity的启动模式
1. launch model
1.1 standard
1.2 singleTop
1.3 singleTask
1.4 singleInstance 总是单独的任务栈
    
2. activity的flag
2.1 Intent.FLAG_ACTIVITY_CLEAR_TOP
2.2 Intent.FLAG_ACTIVITY_NEW_TASK
2.3 Intent.FLAG_ACTIVITY_SINGLE_TOP
2.4 Intent.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS 用户不能通过历史栈回到此activity    
可以通过配置android:taskAffinity="xxx.xxx.xxx"来设置activity的任务栈，默认为包名。

3. IntentFilter
3.1 action
intent中的action要有一个和filter规则中定义的匹配就ok。
3.2 category
intent中的所有category都需要和filter规则中定义的匹配才ok
3.3 data
和action一样。系统默认的data其uri的scheme为content、file。
3.4 action、category和data如果都定义了，那么intent中也必须要将其三个都匹配才行。

## 二、IPC

### IPC简介
inter process communication，就是进程间通信。一个app一般时对应一个进程，一个进程会又多个线程运行。
为CPU最小的调度单元，同一个进程会共享一段内存。

### android 中多进程模式
可以通过设置android:process=":xxx"来实现让activity运行在不同的进程中。

### android IPC基础概念介绍
1. serializable
java中的序列化接口，通过io操作实现序列化和反序列化，方便网络传输。serialVersionUID用于

2. parcelable
android中定义的序列化接口，通过writeToParcel和一系列read方法实现序列化和反序列化。parcelable主要是在
内存中实现序列化的，相比serializable更高效，但是如果通过网络传输相比serializable则更复杂。

3. binder
binder有很多的解释，是android的一个类，是进程间通信的一种方式。在android开发中binder主要用在service中包括aidl和messenger，
而messenger的底层也是aidl，所以通过aidl可以很好的解释binder。使用binder进行进程间传输的对象都必须是实现parcelable接口的。

### IPC方式
1. 使用bundle
四大组件都支持bundle，但是有些情况bundle并不能满足所有数据都能放入bundle。

2. 使用文件共享
android基于linux支持文件的并发读写，有可能不安全。同样使用xml来存储键值对的SharedPreferences也是同样的理由。

3. 使用messenger
messenger通过传送message实现进程间通信，是一个轻量级的IPC方案。它是串行方式处理消息，不支持跨进程方法调用，aidl可以解决这个问题。

4.aidl 
android interface definition language 是一种规范，遵循这哥规范实现进程间通信。支持基本数据类型、String、CharSequence、List（ArrayList）、
Map（HashMap）、Parcelable和AIDL。

服务端的aidl需要考虑权限的限制，不能让所有客户端随意的访问。

5. ContentProvider
ContentProvider底层也是binder。

6. socket
略

### binder链接池
aidl的流程是一个service，一个aidl和一个继承了aidl接口中stud的类，由service在onBind中返回此类。为了避免多个service创建可以采用binder连接池，
服务端只有一个service，通过queryBinder获取相应模块的binder，从而减少服务端的开销。

### 选择合适的IPC
略

## 三、View的事件体系

### View的基础知识
什么是View；View的位置参数；MontionEvent、TouchSlop；VelocityTracker、GestureDetector和Scroller。

### View的滑动
通过scrollTo和scrollBy（简单适合对view内容滑动）、使用动画（简单，适合没有交互的复杂滑动）和改变布局（复杂适合有交互）。
其中动画分视图动画和属性动画，视图动画需要设置fillAfter，否则会回到初始状态。android3.0（api11）之后支持属性动画。

### 弹性滑动
通过Scroller、动画和延时策略。

### View的事件分发机制
事件分发机制类似上级有个问题，一级级下达下去，有最底层的人员处理解决掉。如果最底层的人员不能解决掉，那么就一层层往上抛。
事件是由activity -> window -> decor  -> view -> 子view一级级往下分发，通过dispatchTouchEvent(MotionEvent ev),
onInterceptTouchEvent(MotionEvent ev)和onTouchEvent(MotionEvent ev)三个方法来实现。

### View的滑动冲突
通过外层的onInterceptTouchEvent事件拦截和内部的onTouchEvent事件里调用requestDisallowInterceptTouchEvent拦截事件实现。


## 四、view的工作原理
### 初试ViewRoot和DecorView和WindowManager
``` 
root.setView(view,params,parentView);
```
ViewRoot是连接DecorView和WindowManager的纽带。
View的绘制流程是从ViewRoot的performTraversals开始的（performMeasure、performLayout和performDraw）。

### 理解MeasureSpec
View的尺寸测量通过调用onMeasure方法实现。父View的MeasureSpec和View自身的LayoutParams决定了View的测量结果。

### View的工作流程
measure、layout和draw就是View的工作流程。由最顶层的view调用onMeasure、onLayout和onDraw方法开始执行往下执行
measure、layout和draw。

### 自定义View
不规则的图形继承自View，重写onDraw；继承ViewGroup派生特殊的Layout（比较接近View的底层）；继承特定的View（比如TextView）；继承特定的ViewGroup。


## 五、理解RemoteViews

### RemoteViews的应用
用于跨进程界面更新，NotificationManager和AppWidgetManager（本质是一个BroadCastReceiver）在开发过程中都会用到RemoteViews。

### RemoteViews的内部机制
RemoteViews只支持有限的View，不支持findViewById，使用一系列set方法来设置属性。通过binder在进程间传递，之后通过反射（ReflectionAction）调用View的方法实现界面更新。

### RemoteViews的意义
在跨进程频繁更新界面，而界面又不复杂的的情况下RemoteViews是很好的AIDL替代品。


## 六、Android的Drawable
drawable表示的是一种可以在canvas上进行绘制的概念，可以是图片和颜色。

1. BitmapDrawable
1.1 android:antialias 抗锯齿
1.2 android:dither 抖动效果
1.3 android:tileMode 平铺模式

2. ShapeDrawable

3. LayerDrawable

4. StateListDrawable 状态Drawable集合

5. LevelListDrawable 等级Drawable集合

6. TransitionDrawable 淡入淡出效果

7. InsetDrawable 可以将其它Drawable嵌套在自己当中

8. ScaleDrawable 可以根据自己等级将Drawable缩放到一定比例

9. ClipDrawable 可以根据当前等级裁剪另一个Drawable

10. 自定义Drawable很少用到，因为不能直接在XML中使用

## 七、Android动画深入分析

### View动画

1. 平移(TranslateAnimation)
2. 旋转(RotateAnimation)
3. 缩放(ScaleAnimation)
4. 透明(AlphaAnimation)

### 帧动画

播放一组预先定义好的图片

### 属性动画 
属性动画从android3.0（api 11）开始，老版本兼容可采用nineoldandroids库（其实内部是view动画实现）

1. 常用的几个类为ObjectAnimation、ValueAnimation和AnimationSet

2. 差值器（TimeInterpolator）和估值器（TypeEvaluator）
2.1 差值器根据时间流失的百分比来计算出当前属性值改变的百分比。
2.2 估值器根据属性改变的百分比计算改变后的属性值。
    
3. 属性动画的原理，该对象必须提供属性的get和set方法，动画在looper中执行通过反射调用属性的get和set方法设置属性值。

4. 使用动画的注意事项
4.1 帧动画注意OOM
4.2 内存泄漏，一般是属性动画中的无限循环动画
4.3 兼容性问题，属性动画android 3.0（api 11）才支持的
4.4 不要使用px，尽量都使用dp
4.5 动画元素的交互，view动画的事件是保留在原来的位置，属性动画的事件位置则随view改变和改变
4.6 硬件加速推荐使用，android4.0（api 14）默认开启
    
## 八、理解Window和WindowManager
在android中所有的视图都是通过Window来呈现的，包括Activity、Dialog和Toast。Window是一个抽象类，具体实现是PhoneWindow；
WindowManager用于管理Window的创建和销毁。

### Window和WindowManager
1. WindowManager.LayoutParams 的flag属性
1.1 FLAG_NOT_FOCUSABLE(window不需要获取焦点)
1.2 FLAG_NOT_TOUCH_MODAL (window区域内的点击事件自己处理，区域外的交由底层window处理)
1.3 FLAG_SHOW_WHEN_CLOCKED（window可以显示在锁屏上）
    

2. WindowManager.LayoutParams 的type属性
2.1 应用级window（1-99）
2.2 子window（1000-1999）
2.3 系统级window（2000-2999）定义系统级别的window需要申请user-permission
    
3. WindowManager操控Window是通过添加、删除和更新Window里的view是显现。

### Window的内部机制
每个Window对应一个View和ViewRootImpl，Window和View通过ViewRootImpl建立联系。ViewRootImpl由WindowManagerGlobal（WindowManager的实现类）
创建。最后Window中View的添加、删除和更新通过ViewRootImpl调用WindowManagerService实现，这3个操作都是IPC过程。

### Window的创建

1. Activity的创建
2. Dialog的创建
3. Toast的创建

## 九、四大组件的工作过程

### 四大组件的运行状态

1. Activity
2. Service
3. BroadcastReceiver
4. ContentProvider

### Activity的工作过程
1. 通过Binder机制将创建Activity的消息通知给ActivityManagerService；
2. 根据activity的启动模式判断是否应该创建并将Activity放于栈顶；
3. 判断Activity是否已存在，如果存在置为resume状态，如果不存在重新开启创建；
4. 判断Activity的进程是否创建，没有开始创建，已创建开启Activity；
5. 调用Activity.attach初始化，回调onCreate方法，调用ActivityThread.handleResumeActivity将Activity设置为resume状态。

### Service的工作过程
1. Service启动
![](service启动.jpg)

2. Service绑定
![](service绑定.jpg)

### BroadcastReceiver的工作过程
1. BroadcastReceiver注册
2. BroadcastReceiver发送
3. BroadcastReceiver接受

### ContentProvider的工作过程
ContentProvider的onCrate先于Application的onCreate执行。

## 10、Android的消息机制

### Android消息机制概述
创建Handler需要使用到当前线程的Looper来构建内部消息循环系统，如果当前线程没有Looper则会报错。（可以调用Looper.prepare创建Looper）
Handler创建完毕后，Looper和MessageQueue就可以和Handler系统工作了。通过Handler的post方法将一个Runnable投递到Handler内部的Looper中去处理或则
通过Handler的send方法发送消息，post方法最终也是调用send方法。当Handler的send方法被调用时，它会调用MessageQueue的enqueueMessage方法将这个消息
添加到消息队列中，然后Looper发现有新的消息到来时，就会处理这个消息，最终消息中的Runnable或者Handler的handleMessage方法会被调用。因为Looper所在
的线程时Handler被创建的线程，这样一样业务逻辑就切换到创建Handler的线程中去执行了。

### Android消息机制分析

1. ThreadLocal的工作原理

2. MessageQueue的工作原理

3. Looper的工作原理

4. Handler的工作原理

### 主线程的消息循环

## 11、Android的线程和线程池

### 主线程和子线程
android3.0（api 14）新特性
1. 主线程中不能做网络请求
2. 支持属性动画
3. Fragment替代了ViewGroup

### Android线程的形态
1. AsyncTask
2. HandlerThread
3. IntentService

### Android中的线程池
1. FixedThreadPool 线程数量固定，空闲线程
2. CacheThreadPool
3. ScheduledThreadPool
4. SingleThreadExecutor

## 12、Bitmap的加载和Cache

### Bitmap的高效加载
1. android中通过BitmapFactory的四个方法加载图片：decodeFile、decodeSource、decodeStream、decodeByteArray。

2. 通过BitmapFactory.options来加载所需的尺寸，通过inSampleSize采样实现。
注意采样需要2次decode，而decodeStream采用 FileInputStream 时会出现问题，因为 FileInputStream采用有序的文件流，
会导致第二次decode拿不到stream。解决办法是通过文件流获取文件描述，再通过BitmapFactory.decodeFileDescriptor加载缩放后的图片。

### Android中的缓存策略

1. LruCache(least recent use cache)
LruCache是android3.1提供的缓存类，兼容早起版本可以使用support-v4包。它采用LinkedHashMap以强引用的方式存储外界的缓存对象，提供put和get方法来添加和删除缓存。
当缓存满时LruCache会移除较早使用的缓存对象，然后添加新缓存。

强引用 Object o = new Object()
软引用 SoftReference<Object> sf = new SoftReference(); 内存不够被销毁
弱引用 WeakReference<Object> wf = new WeakReference(); 内存够当触发GC时也会被销毁
虚引用 PhantomReference<Object> pf = new PhantomReference();

2. DiskCache 磁盘缓存

### ImageLoader的使用

## 13、综合技术

### CrashHandler
略

### Multidex来解决方法数越界
略

### Android的动态加载技术
1. 资源的访问
通过调用AssetManager的addAssetPath发放，我们可以将一个apk中的资源加载到Resource对象中，由于addAssetPath隐藏在api中，所以我们只能通过反射。
[addAssetPat(assetManager,dexPath)]
2. activity 生命周期的管理
通过提供生命周期接口，所有的插件Activity都实现此接口，通过代理的Activity调用插件Activity的生命周期方法。
3. 插件ClassLoader管理
为了防止多个ClassLoader加载同一个类造成类型转换错误，在代码中，通过将不通插件的ClassLoader村春在HashMap中，来保证不同插件中的类互不干扰。

### 反编译
略


## 14、JNI和NDK编程

### JNI的开发流程
1. 在java中声明native方法
2. 编译java原文件得到class文件，通过javah命令到处jni头文件
3. 实现jni方法，可以通过C或者是C++
4. 编译so库，可以通过gcc，然后运行使用

### NDK的开发流程
1. 下载和配置NDK
2. 创建android项目，并声明所需的native方法
3. 实现项目中的native方法（test.cpp,Android.mk,Application.mk）
4. 通过ndk-build命令编译产生so文件

### JNI的数据类型和类型签名
略

### JNI调用java方法
先是通过类名找到类，再通过方法名找到方法id，最后就可以调用这个方法了。

## 15、Android的性能优化

### Android性能优化
#### 布局优化
1. include标签
1.1 可以重用布局，配合merge可以减少布局层级。

2. merge标签
2.1 可以减少布局层级。

3. ViewStub 按需加载
3.1 因为ViewStub高宽都是0，所以不会参与布局。通过调用setVisible和inflate后被layout指定的布局替换；它能提高页面初始布局效率，在网络出错的业务场景中比较好用。

4. 线性布局优于相对布局

#### 绘制优化
1. onDraw方法不要创建新对象
2. onDraw方法不要做耗时任务

#### 内存泄漏优化
1. 静态变量引用
2. 单利模式
3. android无限循环动画
#### 响应速度优化和ANR分析
1. Activity 5s
2. BroadcastReceiver 10s
3. service 20s

#### ListView和Bitmap优化
1. 避免在getView中做耗时操作
2. 根据滑动状态来控制任务的执行频率
2.1 使用inSampleSize缩放图片
2.2 android3.0以后可以使用inBitmap复用图片内存
2.3 使用图片缓存

#### 线程优化
1. 线程池可以重用线程避免创建和销毁的开销
2. 避免抢占CPU导致堵塞
    



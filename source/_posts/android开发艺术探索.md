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
    - 系统配置发生变化（屏幕旋转）
    可以配置android:configChanges="orientation|screenSize"来避免重走生命周期，screenSize在api13之后屏幕旋转会导致activity重启。
    - 内存不足（后台activity被杀死）
    如果一个进程中没有四大组件，该进程将很快会被系统杀死，所以一些耗时操作最好时放在service中进行。

### activity的启动模式
1. launch model
    - standard
    - singleTop
    - singleTask
    - singleInstance 总是单独的任务栈
    
2. activity的flag
    - Intent.FLAG_ACTIVITY_CLEAR_TOP
    - Intent.FLAG_ACTIVITY_NEW_TASK
    - Intent.FLAG_ACTIVITY_SINGLE_TOP
    - Intent.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS 用户不能通过历史栈回到此activity
    
可以通过配置android:taskAffinity="xxx.xxx.xxx"来设置activity的任务栈，默认为包名。

3. IntentFilter
    - action
    intent中的action要有一个和filter规则中定义的匹配就ok。
    
    - category
    intent中的所有category都需要和filter规则中定义的匹配才ok
    
    - data
    和action一样。系统默认的data其uri的scheme为content、file。
    
action、category和data如果都定义了，那么intent中也必须要将其三个都匹配才行。
    
    

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


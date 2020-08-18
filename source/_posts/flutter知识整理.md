---
title: flutter知识整理
date: 2020-01-28 20:18:44
categories: flutter
tags:
    - flutter
description: 我从2019年10月左右开始使用flutter开发公司的flutter项目（连连LinkTalk），到现在连连LinkTalk已经开发到1.1.0版本了。刚好现在新型肺炎导致春节假期延长，所以抽时间整理了一下这段时间对flutter的学习和运用。
---

### 学习资料整理
1. [flutter官网](https://flutter.cn/)
flutter官方网站，提供了flutter sdk和dart sdk等下载和官网文档内容。

2. [flutter中文社区](https://flutterchina.club/)
对flutter文档进行了梳理分类，对初学者快熟入门和掌握flutter的整体结果。

3. [flutter：github](https://github.com/flutter/flutter)
flutter的github地址。

### [flutter简介](/flutter/flutter简介)

### [dart语法](/flutter/dart语法)

### flutter整理框架
|- framework（dart框架层）至上而下包括：（material、cupertino）、widget、redering、（gesture、painting、animation）、function

|- engine（C/C++引擎）包括：skia引擎、dart运行时和文本渲染引擎

|- embedder（平台嵌入层）

### flutter渲染过程
1. 系统发出Vsync信号
2. 执行animation阶段：Vsync信号会改变animation的state，而animation的state会影响到widget的尺寸和位置
3. build：即StatelessWidget.build和State.build
4. layout：更新render object的size和position
5. paint：此时RenderObject.paint()被调用

### flutter初始化（runApp(Widget app)）
1. WidgetFlutterBinding.ensureInitialized() 
- 返回WidgetBinding实例，初始化各个binding（WidgetBinding、RenderingBinding、PainingBinding、ScheduleBinding、GestureBinding、ServiceBinding）。

2. WidgetFlutterBinding.attachRootWidget(app)
- 将RenderingBinding实例化的RenderView（继承自RenderObject的最顶层RenderObject）和RenderObjectToWidgetElement（最顶层Element）通过RenderObjectToWidgetAdapter（最顶层的Widget）关联起来。

3. WidgetFlutterBinding.scheduleWarmFrame()
- 此方法调用后会立即执行engine的第一帧渲染而不是等待Vsync信号，之后执行handleBeginFrame和handleDrawFrame回调。

### Widget、Element、RenderObject
1. Widget是对Element的配置和描述，这样Widget可以专心做业务UI实现，不用管element树的维护和渲染工作。
- 生命周期：initState--> didChangeDependence--> build--> 显示--> (didUpdateWidget--> build)--> deactivate--> dispose
 
2. Element用来维护Element tree（增删改查），关联了Widget和RenderObject，由Widget创建并通过自身markNeedsBuild方法将需要重新渲染的Element加入dirtyElement列表。
- 生命周期：initial（widget.createElement方法被调用）--> active（element.mount、renderObject.mount方法被调用，屏幕中展示）--> inactive（widget.rebuild时，通过runType和key判断element需删除element.deactivate方法被调用）--> defunct（element.unmount方法被调用）

3. RenderObject 负责渲染对象的 layout 和 paint 。

### flutter种的组件key
1. 当widget rebuild的时候会迭代element tree，通过runType和key来判断两个element是否相同，如果相同则通过element.build来更新，否则会删除此element生成新的element更新。
2. GlobalKey和LocalKey分别作用全局和当前页。

### flutterUI更新过程
1. 调度到engine请求一帧
- State.setState()--> element.markNeedsBuild()【判断是否需要标记为dirty，active和dirty2个判断】--> 运行到window.scheduleFrame【请求一帧，会判断app状态：resumed活动的，inactive可见不可响应，pause不可见，suspending挂起】

2. Vsync信号之后的渲染
- window.onBeginFrame【一次性回调主要处理有关animation阶段的工作】--> window.onDrawFrame【主要处理build，layout和paint工作】--> 之后清理不需要的Element tree节点

3. 将scene添加到window
- window.render(scene)

### flutter和原生的嵌套
1. 原生嵌套flutter
- 原生项目下建立flutter model进行引用通信
- 将flutter项目编程成库添加到原生项目

2. flutter嵌套原生
- 通过flutter plugin实现

3. 嵌套通信方式
- 通过在初始化flutter页面时的路由传递字符串（原生到flutter传递，无返回值）
- 通过 EventChannel来实现（原生到flutter传递，无返回值）
- 通过 MethodChannel（双向传递，有返回值，普遍用于方法调用）
    - flutter -> android
    - 1）注册 MessageChange("channelName")，调用 setMethodCallHandler 设置回调接收。
    - 2）调用 MessageChange.invokeMethod("methodName","params") 发送消息，
    - 3）channel.invokeMethod底层是调用了ui.window.sendPlatformMessage(channel, message, (ByteData reply)
    - 4）window.sendPlatformMessage 内部调用了native方法 Window_sendPlatformMessage 将消息发送业务交给了C++层。
    
    - android -> flutter
    - 1）注册MessageChange(flutterView,"channelName")，调用 setMethodCallHandler 设置回调接收。
    - 2）调用 channel.invokeMethod("methodName", "params", new MethodChannel.Result() {}发送消息，
    - 3) channel.invokeMethod底层调用了 flutterJni.dispatchPlatformMessage(channel, byteBuffer, position，responseId)
    - 4）flutterJni.dispatchPlatformMessage 内部也是调用了native方法实现消息发送。
    
- 通过 BaseMethodChannel（双向传递，有返回值，普遍用于数据传输）

4. flutter和原生的页面调用
- 原生打开flutter页面（创建activity通过setContentView加载flutterView，通过viewControl调起flutterViewControl）
- flutter打开原生页面（通过MessageChannel调用原生方法打开原生页面）

5. flutter app的页面栈管理
- 阿里
- 

### flutter业务相关
#### 页面跳转

### flutter编译


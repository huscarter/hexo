---
title: flutter知识整理
date: 2020-01-28 20:18:44
categories: flutter
tags:
     - flutter
description: 我从2019年10月左右开始学习flutter，11月开始开发公司的flutter项目（连连LinkTalk），到现在连连LinkTalk已经开发到1.1.0版本了。刚好现在新型肺炎导致春节假期延长，所以抽时间整理了一下这段时间对flutter的学习和运用。
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

### flutter运行原理

#### flutter整理框架
|- framework（dart框架层）至上而下包括：（material、cupertino）、widget、redering、（gesture、painting、animation）、function

|- engine（C/C++引擎）包括：skia引擎、dart运行时和文本渲染引擎

|- embedder（平台键入层）

#### flutter渲染过程
1. 系统发出Vsync信号
2. 执行animation阶段：vsync信号会改变animation的state，而animation的state会影响到widget的尺寸和位置
3. build：即StatelessWidget.build和State.build
4. layout：更新render object的size和position
5. paint：此时RenderObject.paint()被调用

#### flutter初始化（runApp(Widget app)）
1. WidgetFlutterBinding.ensureInitialized() 
 - 返回WidgetBinding实例，初始化各个binding（WidgetBinding、RenderingBinding、PainingBinding、ScheduleBinding、GestureBinding、ServiceBinding）。

2. WidgetFlutterBinding.attachRootWidget(app)
 - 将RenderingBinding实例化的RenderView（继承自RenderObject的最顶层RenderObject）和RenderObjectToWidgetElement（最顶层Element）通过RenderObjectToWidgetAdapter（最顶层的Widget）关联起来。

3. WidgetFlutterBinding.scheduleWarmFrame()
 - 此方法调用后会立即执行engine的第一帧渲染而不是等待Vsync信号，之后执行handleBeginFrame和handleDrawFrame回调。

#### Widget、Element、RenderObject
1. Widget是对Element的配置和描述，这样Widget可以专心做业务UI实现，不用管element树的维护和渲染工作。

2. Element用来维护Element tree（增删改查），关联了Widget和RenderObject，由Widget创建并通过自身markNeedsBuild方法将需要重新渲染的Element加入dirtyElement列表。

3. RenderObject负责渲染对象的layout和paint。

#### flutterUI更新过程
1. 调度到engine请求一帧
 - State.setState() --> element.markNeedsBuild()【判断是否需要标记为dirty，active和dirty2个判断】--> 运行到window.scheduleFrame【请求一帧，会判断app状态：resumed活动的，inactive可见不可响应，pause不可见，suspending挂起】

2. Vsync信号之后的渲染
 - window.onBeginFrame【一次性回调主要处理有关animation阶段的工作】 --> window.onDrawFrame【主要处理build，layout和paint工作】--> 之后清理不需要的Element tree节点

3. 将scene添加到window
 - window.render(scene)





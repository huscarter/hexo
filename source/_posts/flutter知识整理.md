---
title: flutter知识整理
date: 2020-01-28 20:18:44
categories: flutter
tags:
    - flutter
description: 我从2019年10月左右开始使用flutter开发公司的flutter项目（连连LinkTalk），到现在连连LinkTalk已经开发到1.1.0版本了。刚好现在新型肺炎导致春节假期延长，所以抽时间整理了一下这段时间对flutter的学习和运用。
---

## [flutter简介](/flutter/flutter简介)

## [dart语法](/flutter/dart语法)

## flutter框架相关

### flutter 框架概括
| 层级名称              | 主要功能                                                     |
| --------------------- | ------------------------------------------------------------ |
| framework(dart框架层) | （material、cupertino）、widget、redering、（gesture、painting、animation）、function |
| engine(C/C++引擎)     | Skia引擎、Dart运行时和文本（Text）渲染引擎                   |
| embedder(平台嵌入层)  | Render Surface Setup、Native Plugins、Packages、Thread Setup、Event Loop |

### flutter渲染过程
1. 系统发出Vsync信号
2. 执行animation阶段：Vsync信号会改变animation的state，而animation的state会影响到widget的尺寸和位置
3. build：即StatelessWidget.build和State.build
4. layout：更新render object的size和position
5. paint：此时RenderObject.paint()被调用

### flutter初始化

主要介绍runApp(Widget app)方法做了哪些工作

1. WidgetFlutterBinding.ensureInitialized() 
返回WidgetBinding实例，初始化各个binding（WidgetBinding、RenderingBinding、PainingBinding、ScheduleBinding、GestureBinding、ServiceBinding）。

2. WidgetFlutterBinding.attachRootWidget(app)
将RenderingBinding实例化的RenderView（继承自RenderObject的最顶层RenderObject）和RenderObjectToWidgetElement（最顶层Element）通过RenderObjectToWidgetAdapter（最顶层的Widget）关联起来。

3. WidgetFlutterBinding.scheduleWarmFrame()
此方法调用后会立即执行engine的第一帧渲染而不是等待Vsync信号，之后执行handleBeginFrame和handleDrawFrame回调。

### flutterUI更新过程
1. 调度到engine请求一帧
1.1 State.setState()--> element.markNeedsBuild()【判断是否需要标记为dirty，active和dirty2个判断】--> 运行到window.scheduleFrame【请求一帧，会判断app状态：resumed活动的，inactive可见不可响应，pause不可见，suspending挂起】
2. Vsync信号之后的渲染
2.1 window.onBeginFrame【一次性回调主要处理有关animation阶段的工作】--> window.onDrawFrame【主要处理build，layout和paint工作】--> 之后清理不需要的Element tree节点
3. 将scene添加到window
3.1 window.render(scene)

## flutter api相关

### Widget、Element、RenderObject

1. Widget是对Element的配置和描述，这样Widget可以专心做业务UI实现，不用管element树的维护和渲染工作。
   1.1 生命周期：initState--> didChangeDependence--> build--> 显示--> (didUpdateWidget--> build)--> deactivate--> dispose

2. Element用来维护Element tree（增删改查），关联了Widget和RenderObject，由Widget创建并通过自身markNeedsBuild方法将需要重新渲染的Element加入dirtyElement列表。
   2.1 生命周期：initial（widget.createElement方法被调用）--> active（element.mount、renderObject.mount方法被调用，屏幕中展示）--> inactive（widget.rebuild时，通过runType和key判断element需删除element.deactivate方法被调用）--> defunct（element.unmount方法被调用）

3. RenderObject 负责渲染对象的 layout 和 paint 。

### flutter组件的key

1. 当widget rebuild的时候会迭代element tree，通过runType和key来判断两个element是否相同，如果相同则通过element.build来更新，否则会删除此element生成新的element更新。
2. GlobalKey和LocalKey分别作用全局和当前页。

### flutter i18n、l10n

i18n（国际化标准）是internationalization的简称，l10n（本地化实现）是localization的简称。i18n为实现l10n提供标准化框架。下面将分步教你如何实现一个l10n，让你的app可以根据系统语言来展示不同文本。

1. 添加pubspec.yaml配置

   ```
   dependencies:
     flutter_localizations:
       sdk: flutter
     intl: ">=0.16.1 <=0.17.0"
     flutter_localized_locales: ">=1.1.1 <=2.0.0"
   
   ...
   
   # The following section is specific to Flutter.
   flutter:
     generate: true // 开启自动生成
   ```

2. 项目里设置国际化语言适配

   ```
   return MaterialApp(
   title: 'xxx',
   localizationsDelegates :[
   	GlobalMaterialLocalizations.delegate,// 指定本地化的字符串和一些其他的值
     GlobalCupertinoLocalizations.delegate,// 对应的Cupertino风格
     GlobalWidgetsLocalizations.delegate,// 指定默认的文本排列方向,由左到右或由右到左
   ],
   // 适配的国际语言
   supportedLocales :[
   	Locale('en'),
     Locale('zh')
   ],
   debugShowCheckedModeBanner: false,
   ```

3. 建立l10n配置文件：l10n.yaml

   ```
   arb-file: lib/l10n // 指定arb文件目录
   template-arb-file: intl_en.arb
   output-localization-file: app_localizations.dart //指定自动生成的dar文件名
   output-class: AppLocalizations // 指定自动生成的dart文件类名
   preferred-supported-locales:
     - en
   use-deferred-loading: false //开启延迟加载
   ```

4. 建立本地语言文件

   4.1 根据所需的国际语言在lib/l10n目录下建立相应的文件，这里我们创建intl_en.arb和intl_zh.arb两个。

   ```
   // intl_en.arb
   {
     "timePicker":"time picker",
     "@timePicker":{
       "description":"The content of the butter for picker time."
     }
   }
   ```

   ```
   // intl_zh.arb
   {
     "timePicker":"选择时间"
   }
   ```

   4.2 让编译器自动生成本地化代码

   ```
   /// termial进入到你的项目运行命令
   flutter analyze
   /// 如果执行成功会在.dart_tool/flutter_gen/gen_l10n下生成相应的类
   ```

5. 在项目里引用本地化字符串

   5.1 <font color="red">更改项目原国际化适配器(使用自动生成的)</font>

   ```
   return MaterialApp(
   title: 'xxx',
   localizationsDelegates :AppLocalizations.localizationsDelegates,
   // 适配的国际语言
   supportedLocales :AppLocalizations.supportedLocales,
   debugShowCheckedModeBanner: false,
   ```

   5.2 引用本地化字符串

   ```
   MaterialButton(
     child: Text(AppLocalizations.of(context).timePicker), // 本地化字符串引用
     onPressed: () {
     	// todo something.
     },
   ),
   ```

6. iOS 适配问题

   iOS需要在Info.plist 里手动声明适配本地化语言，直接在Info.plist中添加如下代码：

   ```
   <key>CFBundleLocalizations</key>
     <array>
     <string>zh_CN</string>
     <string>en</string>
   </array>
   ```

7. 运行效果

   安装好app，将手机系统语言中英文切换再打开app试试。

## flutter混合开发模式
### 原生嵌套flutter
1. 原生项目下建立flutter model进行引用通信
2. 将flutter项目编程成库添加到原生项目

### flutter嵌套原生

1. 官方默认模式

### 嵌套通信方式
1. 通过在初始化flutter页面时的路由传递字符串（原生到flutter传递，无返回值）

2. 通过 EventChannel来实现（原生到flutter传递，无返回值）

3. 通过 MethodChannel（双向传递，有返回值，普遍用于方法调用）

   3.1 flutter -> android

   ```
   注册 MethodChannel("channelName")，调用 setMethodCallHandler 设置回调接收。
   ```

   ```
   调用 MethodChannel.invokeMethod("methodName","params") 发送消息，
   invokeMethod底层是调用了ui.window.sendPlatformMessage(channel, message, reply)。
   ```

   ```
   sendPlatformMessage 内部调用了native方法 Window_sendPlatformMessage 
   将消息发送业务交给了C++层。
   ```

   3.2 android -> flutter

   ```
   注册MessageChange(flutterView,"channelName")，调用 setMethodCallHandler 设置回调接收。
   ```

   ```
   调用 channel.invokeMethod("methodName", "params", new MethodChannel.Result())发送消息，
   invokeMethod底层调用了 flutterJni.dispatchPlatformMessage(channel, byteBuffer, 
   position，responseId)。
   ```

   ```
   flutterJni.dispatchPlatformMessage 内部也是调用了native方法实现消息发送。
   ```

4. 通过 BaseMethodChannel（双向传递，有返回值，普遍用于数据传输）

### flutter和原生的页面调用
1. 原生打开flutter页面（创建activity通过setContentView加载flutterView，通过viewControl调起flutterViewControl。
2. flutter打开原生页面（通过MessageChannel调用原生方法打开原生页面）。

### flutter app的页面栈管理
1. flutter boost
2. xx



## flutter编译



## flutter 热更新



## 参考

### [flutter官网](https://flutter.cn/)
### [flutter中文社区](https://flutterchina.club/)
### [flutter.github](https://github.com/flutter/flutter)

---
title: flutter技术介绍
date: 2019-10-31 09:29:44
categories: flutter
tags:
     - flutter
description: 简要介绍了Flutter相比目前跨平台开发框架的优缺点，和造成的原因。
---

## Flutter调研

### 简介

Flutter 是 Google推出并开源的移动应用开发框架，主打跨平台、高保真、高性能。开发者可以通过 Dart语言开发 App，一套代码同时运行在 iOS 和 Android平台。同时Flutter也可以开发web和PC端应用（没错PC也是可以）。 Flutter提供了丰富的组件、接口，开发者可以很快地为 Flutter添加 native扩展。同时 Flutter还使用 Native引擎渲染视图，这无疑能为用户提供良好的体验。

### Flutter优势和不足

#### 优势
1. 跨平台自绘引擎
Flutter既不使用WebView，也不使用操作系统的原生控件。 Flutter使用自己的高性能渲染引擎来绘制widget。Flutter使用Skia作为其2D渲染引擎，而Skia是跨平台的。目前Flutter目前默认支持iOS、Android、Fuchsia移动平台。

2. 高性能
2.1 Flutter采用Dart语言开发,Dart支持 JIT和AOT编译，在JIT模式下速度与 JavaScript基本持平；当以 AOT模式运行时，速度远快于JavaScript，所以秒杀RN和WEEX。
2.2 Flutter使用自己的渲染引擎来绘制UI，布局数据等由Dart语言直接控制，所以在布局过程中不需要像RN那样要在JavaScript和Native之间通信。

3. 采用Dart语言开发
3.1 开发效率高
3.1.1 在开发阶段采用，采用JIT模式，这样就避免了每次改动都要进行编译。
3.1.2 发布时可以通过AOT生成高效的ARM代码以保证应用性能。
3.2 高性能
由Dart的AOT实现。
3.3 快速内存分配
Dart的开发团队很多来自Chrome，而Chrome V8的JavaScript引擎在内存分配上是业界良心。
3.4 类型安全
有利于在编译前发现问题。
3.5 热度极高
目前开发第一大语言python在github上的star是56k，kotlin是29k，而flutter居然由75k。

#### 不足
1. 社区发展不充分
目前主要flutter社区是flutter中文网、github、stackoverflow和flutter官网。本土社区和插件提供不足。

2. 框架不够稳定和完善
2.1 目前github遗留5000+的issue没有关闭（已关闭17k+）
2.2 一些原生app现有api在flutter上没有实现。（比如appbar去阴影，浏览器组件功能不及webview组件之类的）

### Flutter对link 的IM开发调研

#### 原生API提供
1. 组件
以widget为基础提供文、图、单复选框、输入框、表单和进度条等，足以满足一般功能需求，复杂需求可自定义widget。

2. 布局
提供线性、弹性、流式布局和层叠布局，基本满足各种业务需求。

3. 容器组件
提供listview、scrollview、gridview、scaffold、tabBar等组件，相对Android和iOS略少。

#### 第三方库支持
1. 支持不够完善
目前有些第三库支持flutter（如url_launcher），但大多数尚未发布支持flutter版本。有些提供flutter版本，但是由于flutter是18年发布至今不久，目前提供的flutter package功能尚不稳定和完善。

## Flutter调研

### 简介
Flutter 是 Google推出并开源的移动应用开发框架，主打跨平台、高保真、高性能。开发者可以通过 Dart语言开发 App，一套代码同时运行在 iOS 和 Android平台。同时Flutter也可以开发web和PC端应用（没错PC也是可以）。 Flutter提供了丰富的组件、接口，开发者可以很快地为 Flutter添加 native扩展。同时 Flutter还使用 Native引擎渲染视图，这无疑能为用户提供良好的体验。

### Flutter优势和不足

#### 优势
1. 跨平台自绘引擎
Flutter既不使用WebView，也不使用操作系统的原生控件。 Flutter使用自己的高性能渲染引擎来绘制widget。Flutter使用Skia作为其2D渲染引擎，而Skia是跨平台的。目前Flutter目前默认支持iOS、Android、Fuchsia移动平台。

2. 高性能
2.1 Flutter采用Dart语言开发,Dart支持 JIT和AOT编译，在JIT模式下速度与 JavaScript基本持平；当以 AOT模式运行时，速度远快于JavaScript，所以秒杀RN和WEEX。

2.2 Flutter使用自己的渲染引擎来绘制UI，布局数据等由Dart语言直接控制，所以在布局过程中不需要像RN那样要在JavaScript和Native之间通信。

3. 采用Dart语言开发
3.1 开发效率高
3.1.1 在开发阶段采用，采用JIT模式，这样就避免了每次改动都要进行编译。
3.1.2 发布时可以通过AOT生成高效的ARM代码以保证应用性能。

3.2 高性能
由Dart的AOT实现。

3.3 快速内存分配
Dart的开发团队很多来自Chrome，而Chrome V8的JavaScript引擎在内存分配上是业界良心。

3.4 类型安全
有利于在编译前发现问题。

3.5 热度极高
目前开发第一大语言python在github上的star是56k，kotlin是29k，而flutter居然由75k。

#### 不足
1. 社区发展不充分
目前主要flutter社区是flutter中文网、github、stackoverflow和flutter官网。本土社区和插件提供不足。

2. 框架不够稳定和完善
2.1 目前github遗留5000+的issue没有关闭（已关闭17k+）
2.2 一些原生app现有api在flutter上没有实现。（比如appbar去阴影，浏览器组件功能不及webview组件之类的）

### Flutter对link 的IM开发调研

#### 原生API提供
1. 组件
以widget为基础提供文、图、单复选框、输入框、表单和进度条等，足以满足一般功能需求，复杂需求可自定义widget。

2. 布局
提供线性、弹性、流式布局和层叠布局，基本满足各种业务需求。

3. 容器组件
提供ListView、ScrollView、GridView、Scaffold、TabBar等组件，相对Android和iOS略少。

#### 第三方库支持
1. 支持不够完善
目前有些第三库支持flutter（如url_launcher），但大多数尚未发布支持flutter版本。有些提供flutter版本，但是由于flutter是18年发布至今不久，目前提供的flutter package功能尚不稳定和完善。

2. 可自行开发第三方库
flutter支持开发者自己引用第三方库，然后分别针对不同平台做相应的开发。

### 结论
1. Flutter基本可以满足目前产品的功能需求
自身提供的api对普通需求完全可以满足；对于IM和其他第三方库的引用可以选择第三方的flutter package，或者自己进行第三方fluter package封装调用。

2. 风险
对于一些复杂的功能、样式有可能会有一定的效果丢失，或者会增加实现代价。

3. 目前实现了flutter版本的融云单聊通信。


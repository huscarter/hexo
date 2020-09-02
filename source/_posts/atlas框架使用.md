---
title: atlas框架使用
date: 2018-03-09 14:29:44
categories: android
tags:
     - android
description: Atlas是一个Android客户端容器框架，主要提供了组件化、动态性、解耦化的支持。支持在编码期、Apk运行期以及后续运维修复期的各种问题。
---

## 插件化开发

1. 对于宿主只做版本控制，logo和各个业务bundle的整合工作，保持轻便。宿主需要配置atlas，配置见app/build.gradle。

2. 业务bundle负责实现各个业务逻辑。内库引用如果是公共库，即其他bundle也可能需要引用需要使用providedCompile，
否则在宿主编译时会发生资源重复错误。如果是bundle独有的lib可以不用配置atlas，直接通过compile引用。

3. 业务bundle之间不可相互引用，只能由宿主关联，页面的调用和传值使用插件化的方式。功能实现请遵守library方式实现，
比如对R.id.xxx的判断不能使用switch的方式。

4. 为了实现业务bundle可以脱离宿主自行编译请在build.gradle和代码里实现 library 和 application 的相关逻辑，使用
gradle.properties的配置进行切换。

5. atlas编译会有缓存，如果你改动过了 AndroidManifest 请记得clean项目，否则AndroidManifest的改动不会生效。

## atals实现原理 

#### atlas 各个实现模块

1. hack toolkit & verifier。
包括了容器所需的所有系统层面的注入和hack的工具类初始化和校验，容器启动时先校验设备是否支持容器运行，不支持则采取降级并记录原因。

2. Bundle Framework（BundleClassLoader 和 AssetPatch）
负责bundle的安装、更新、操作以及管理整个bundle的生命周期。

3. runtime层 (清单管理、版本管理、以及系统代理三大块)
清单列表纪录各个bundle的信息，版本管理负责检查代码执行时运行正确的代码，系统代理包括DelegateClassLoader（负责路由class加载到各个bundle内部）
和DelegateResource（负责资源查找时能够找到bundle内的资源）。

4. 对外接入层（AtlasBridgeApplication）
AtlasBridgeApplication替代项目里的application先执行，完成atlas的初始化，之后在加载项目的application。atlas内部实现了
multidex功能，支持build.gradle的配置开关。


#### 运行加载过程

1. AtlasBridgeApplication.attachBaseContext
1.1 反射并构造BridgeApplicationDelegate的实例
1.2 执行BridgeApplicationDelegate的attachBaseContext方法

2. BridgeApplicationDelegate. attachBaseContext
2.1 hook之前的准备工作
2.2 回调预留接口
2.3 初始化atlas
2.4 处理provider

3. AtlasHacks.defineAndVerify
3.1 动态加载class
3.2 动态加载资源
3.3 处理四大组件 能够让动态代码中的四大组件在Android上正常跑起来。

4. 回调预留接口
4.1 BridgeApplicationDelegate#attachBaseContext()方法里回调build.gradle中配置的方法。

5. atlas.init
5.1 首先是读取manifest中的配置数据multidexEnable和mRealApplicationName，这两个数据也是在编译期由atlas插件写到manifest中的。
5.2 对系统关键节点进行了hook。

## 文档
1. [官方文档](https://alibaba.github.io/atlas/)
2. [框架说明文档](https://alibaba.github.io/atlas/principle-intro/Runtime_principle.html)



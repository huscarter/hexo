---
title: weex知识梳理
date: 2020-10-10 09:29:44
categories: web
tags:
     - weex
description: 是使用流行的 Web 开发体验来开发高性能原生应用的框架。
---

## 简介

weex 致力于使开发者能基于通用跨平台的 Web 开发语言和开发经验，来构建 Android、iOS 和 Web 应用。简单来说，在集成了 WeexSDK 之后，你可以使用 JavaScript 语言和前端开发经验来开发移动应用。

这篇文章主要讲述了weex的优缺点，weex初始开发环境的安装、如何运行你的weex程序以及原生如何加载weex，读完这些你将对weex开发有一个初步的认识。

## weex的优缺点

### 优势
1. 跨平台开发
2. 天然支持热修复
3. 支持第三前端框架

### 不足
1. 社区资源不足
2. 已共享给apache，目前apache的weex项目管理不太积极
3. 运行效率低于flutter
4. 调试效率低于原生开发

## 开发框架
### Rax
Rax 是用于构建移动跨端应用的渐进式 React 框架。但是目前暂不推荐通过 Weex 开发原生应用，新的高性能原生框架 Kraken 即将发布。

### Vue
Vue是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。
1. [Vue使用](/web/vue使用梳理)

## 设置开发环境
### windows
略

### OSX
1. [安装node.js](https://nodejs.org/en/download/)

2. 安装weex-clid
```
sudo chmod -R 777 /usr/local/lib/node_modules/
npm i -g weex-toolkit // 如果安装出错大概率因权限问题，使用sudo
weex -v // 查看当前weex工具版本
```

3. 初始化项目
```
weex create my-project
```

4. 编译项目
```
cd my-project
npm install
npm run build
```

5. 运行项目
```
npm start
```

6. 增加移动端平台（请想自行安装好移动端（Android、iOS）的开发环境）
```
weex platform add ios // 增加iOS平台
weex platform remove ios // 增加Android平台
```

7. 在移动端运行
```
weex run ios // 运行在iOS模拟器
weex run android // 运行在Android模拟器
```

## iOS端创建weex项目
略

## Android端创建weex项目
### 创建Android项目
略

### 增加weex依赖
1. build.gradle
```
// 以下两个 weex_sdk 版本二选一
implementation 'org.apache.weex:sdk:0.28.0'
//implementation 'org.apache.weex:sdk_legacy:0.28.0'

// fastjson
implementation 'com.alibaba:fastjson:1.1.46.android'

//support library dependencies
implementation 'com.android.support:recyclerview-v7:23.1.1'
implementation 'com.android.support:support-v4:23.1.1'
implementation 'com.android.support:appcompat-v7:23.1.1'
```

2. AndroidManifest.xml中
```
<!-- 网络 -->
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<!-- sd卡读写 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
```

3. 推荐在Application中初始化sdk
```
InitConfig config = new InitConfig.Builder()
					//图片库接口
    				.setImgAdapter(new FrescoImageAdapter())
    				//网络库接口
    				.setHttpAdapter(new InterceptWXHttpAdapter())
    				.build();
WXSDKEngine.initialize(applicationContext,config);
```

4. 创建WXSDKInstance
```
public class WXActivity extends AppCompatActivity implements IWXRenderListener {
  WXSDKInstance mWXSDKInstance;
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    mWXSDKInstance = new WXSDKInstance(this);
    mWXSDKInstance.registerRenderListener(this);
    String pageName = "WXActivity";
    // 一般本地发开将前端weex项目build之后的js文件放入Android项目的assets目录，之后通过WXSDKInstance加载即可
    String bundleUrl = "file://assets/dist/index.js";
    mWXSDKInstance.renderByUrl(pageName, bundleUrl, null, null,WXRenderStrategy.APPEND_ASYNC);
  }
  @Override
  public void onViewCreated(WXSDKInstance instance, View view) {
    setContentView(view);
  }
  @Override
  public void onRenderSuccess(WXSDKInstance instance, int width, int height) {
  }
  @Override
  public void onRefreshSuccess(WXSDKInstance instance, int width, int height) {
  }
  @Override
  public void onException(WXSDKInstance instance, String errCode, String msg) {
  }
  @Override
  protected void onResume() {
    super.onResume();
    if(mWXSDKInstance!=null){
      mWXSDKInstance.onActivityResume();
    }
  }
  @Override
  protected void onPause() {
    super.onPause();
    if(mWXSDKInstance!=null){
       mWXSDKInstance.onActivityPause();
    }
  }
  @Override
  protected void onStop() {
    super.onStop();
    if(mWXSDKInstance!=null){
      mWXSDKInstance.onActivityStop();
    }
  }
  @Override
  protected void onDestroy() {
    super.onDestroy();
    if(mWXSDKInstance!=null){
      mWXSDKInstance.onActivityDestroy();
    }
  }
}
```

5. Android设置weex调用的module
   5.1 创建Android端module
```
public class WXNavigator extends WXModule {
    /**
     * 对外方法定义
     */
    public void push(String url){
        // 自定义业务逻辑
    }
}
```

   5.2 注册module
```
WXSDKEngine.registerModule("moduleName",WXNavigator.class);
```

6. Android设置weex调用的component
```
同module设置，调用api改为component
```

7. weex端使用modlue
```
// 引入
const native = _.weexRequire('@weex-module/moduleName')
// 方法调用
native.push('/main/home')
```



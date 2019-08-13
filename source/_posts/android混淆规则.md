---
title: android混淆规则
date: 2019-02-14 22:09:00
categories: android
tags:
     - android
description: 为了应用的安全，一般我们会对apk进行混淆打包和加固。混淆是在release出apk之前做的操作，而加固是对release的apk再次施加的安全措施。一般的公司对加固并不是强制要求的，但是添加混淆却是十分必要的，它是防止我们apk被反编译获取源码的一道基础防线。
---

## 混淆配置
```
android{

    buildTypes {
        debug{
            // 因为混淆会增加编译速度，所以一般在debug模式下不开启混淆 
        }
        release {
                buildConfigField "boolean", "LOG_DEBUG", "false" //不显示log
                minifyEnabled true // 开启混淆
                shrinkResources true // 开启资源压缩
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro' // 配置混淆规则文件
                signingConfig signingConfigs.config // 证书配置
        }
    }
}
```

## 混淆规则

### 不混淆某个类
```
-keep public class name.huihui.example.Test { *; }
```
### 不混淆某个类的子类
```
-keep public class * extends name.huihui.example.Test { *; }
```
### 不混淆所有类名中包含了“model”的类及其成员
```
-keep public class **.*model*.** {*;}
```
### 不混淆某个接口的实现
```
-keep class * implements name.huihui.example.TestInterface { *; }
```
### 不混淆某个类的构造方法
```
-keepclassmembers class name.huihui.example.Test { 
    public <init>(); 
}
```
### 不混淆某个类的特定的方法
```
-keepclassmembers class name.huihui.example.Test { 
    public void test(java.lang.String); 
}
```
### 不混淆某个类的内部类
```
-keep class name.huihui.example.Test$* {*;}
```
### 一颗星表示只是保持该包下的类名，而子包下的类名还是会被混淆；两颗星表示把本包和所含子包下的类名都保持；
```
-keep class com.suchengkeji.android.ui.*
-keep class com.suchengkeji.android.ui.**
```

### 用以上方法保持类后，你会发现类名虽然未混淆，但里面的具体方法和变量命名还是变了，如果既想保持类名，又想保持里面的内容不被混淆，我们就需要以下方法了
### 不混淆某个包所有的类
```
-keep class com.suchengkeji.android.bean.** { *; }
```

### Proguard通配符
```
<field>:匹配类中的所有字段
<method>:匹配类中所有的方法
<init>:匹配类中所有的构造函数
*:匹配任意长度字符，不包含包名分隔符(.)
**:匹配任意长度字符，包含包名分隔符(.)
***:匹配任意参数类型
```

## 常用的混淆例子

### 代码混淆压缩比，在0~7之间，默认为5，一般不做修改
```
 -optimizationpasses 5
```

### 混淆时采用的算法
```
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/*
```

### 混合时不使用大小写混合，混合后的类名为小写
```
-dontusemixedcaseclassnames
```

### 注解
```
-keepattributes *Annotation*
```

### R文件下面的资源
```
-keep class **.R$* {*;}
```

### 本地的native方法（JNI）
```
-keepclasseswithmembernames class * {
    native <methods>;
}
```
### 反射
```
-keep class pageName{*;}
```

### JavaBean中的泛型
```
-keepattributes Signature
```

### JavaBean（如果使用了Gson进行解析Json字符串，就需要添加JavaBean的混淆规则，因为Gson使用了反射的原理）
```
-keep class PackageName.**
-keep class PackageName.**{*;}
```

### 保留枚举类不被混淆
```
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}
```

### Parcelable序列化和Creator静态成员变量
```
-keep class * implements android.os.Parcelable {
    public static final android.os.Parcelable$Creator *;
}
```

### Serializable序列化
```
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    !static !transient <fields>;
    !private <fields>;
    !private <methods>;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}

```
### WebView
```
-keepclassmembers class fqcn.of.javascript.interface.for.Webview {
   public *;
}
-keepclassmembers class * extends android.webkit.WebViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String);
}
-keepclassmembers class * extends android.webkit.WebViewClient {
    public void *(android.webkit.WebView, jav.lang.String);
}
```

### Gson
```
# Gson specific classes
-keep class sun.misc.Unsafe { *; }
-keep class com.google.gson.stream.** { *; }
-keep class com.google.gson.examples.android.model.** { *; }
-keep class com.google.gson.** { *;}
```

### JSON
```
-keepattributes Signature
-dontwarn com.alibaba.fastjson.**
-keep class com.alibaba.fastjson.**{*; }
```

### ButterKnife
```
-keep class butterknife.** { *; }
-dontwarn butterknife.internal.**
-keep class **$$ViewBinder { *; }
-keepclasseswithmembernames class * {    
   @butterknife.* <fields>;
}
-keepclasseswithmembernames class * {    
   @butterknife.* <methods>;
}
```

### RxJava
```
-dontwarn rx.**
-keepclassmembers class rx.** { *; }
# retrolambda
-dontwarn java.lang.invoke.*

```

### retrofit
```
-dontwarn retrofit2.**
-keep class retrofit2.** { *; }
```

### okhttp
```
-dontwarn okhttp3.**
-keep class okhttp3.**{*;}
-keep interface okhttp3.**{*;}
```

### okio
```
-dontwarn okio.**
-keep class okio.**{*;}
-keep interface okio.**{*;}
```

### glide
```
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** {   
  **[] $VALUES; 
  public *;
}
```







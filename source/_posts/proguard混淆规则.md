---
title: proguard混淆规则
date: 2020-11-19 10:29:44
categories: android
tags: 
    - proguard
description: 介绍了Android项目代码混淆所用的知识点，让你Android混淆出包更省心、更放心。
---

因java的字节码很容易反编译，为了保护我们的代码安全，我们可以对.class文件进行代码混淆。那么代码混淆规则该怎么写，proguard提供了哪些配置项以及它们有什么作用，我们接着往下看。

## proguard工作原理

proguard只会读取jars、wars、zips和ears等文件格式的文件，并对它们进行shrinking、optimizes、obfuscates和preverifies处理。

### shrinking

proguard从给定的程序入口（比如main函数）出发，然后递归所有的class和class member，将没有用到的class和class member移除。

### optimizes

代码优化，非入口的classes和methods可能会变成private、static、final，没有用的参数也会被删除，一些函数会被内联。

### obfuscates

将非入口的classes和class member进行重命名。

### preverifies

在classes中添加预校验信息。这一步proguard不需知道class是否为入口类。



## proguad的混淆指令

1. 指定混淆压缩比

   ```
   # 混淆压缩比为5（默认是5，一般在0～7之间）
   -optimizationpasses 5
   ```

2. 指定混淆算法

   ```
   # 混淆时采用的算法，后面配置的是过滤项
   -optimizations !code/simplification/arithmetic,!code/simplification/cast,!field/*,!class/merging/*,!code/allocation/variable
   ```

3. 是否开启优化

   ```
   # 不优化输入的类文件。注意：此指令开启后optimizationpasses和optimizations将失去期效果
   -dontoptimize
   ```

4. 预校验处理

   ```
   # 此处为关闭预校验，Android的混淆不需要做预校验处理，关闭预校验可以加快混淆速度
   -dontpreverify
   ```

5. 指定是否生成混淆映射文件

   ```
   # 生成映射文件，包含类型->混淆后类型的映射关系
   -verbose
   ```

6. 是否使用大小写

   ```
   # 不使用大小写，混淆后类名为小写
   -dontusemixedcaseclassnames
   ```

7. 非公共库混淆

   ```
   #不忽略非公共库的类
   -dontskipnonpubliclibraryclasses
   ```

8. keep

   8.1 作用范围可以是类名

   ```
   # 所有继承activity类的类名不混淆
   -keep public class * extends android.app.Activity
   # 所有com.whh.activity包下（不包含子包）的类类名不混淆
   -keep public class com.whh.activity.*
   # 所有com.whh.activity包下（包含子包）的类类名不混淆
   -keep public class com.whh.activity.**
   ```

   8.2 作用类名以及类的成员

   ```
   # 所有继承activity类的类名和类成员都不混淆
   -keep public class * extends android.app.Activity{*;}
   # 所有com.whh.activity包下（不包含子包）的类类名和类成员都不混淆
   -keep public class com.whh.activity.*{*;}
   # 所有com.whh.activity包下（包含子包）的类类名和类成员都不混淆
   -keep public class com.whh.activity.**{*;}
   ```

9. keepclassmembers

   此指令只作用于类成员

   ```
   # 所有类的参数类型为JSONObject的构造方法不混淆
   -keepclassmembers class *{
   	public <init> (org.json.JSONObject);
   }
   ```

10. keepclasseswithmembers

    <font color="red">此指令和keep很像，不同的一点是如果此指令定义的过滤条件没有匹配到整条指令将失效。keep是没匹配到的部分会失效，其他匹配到的部分依然起作用。</font>

    10.1 作用类名

    ```
    # 所有继承activity类的类名不混淆
    -keepclasseswithmembers class * extends android.app.Activity
    ```

    10.2 作用类名和类成员

    ```
    # 所有继承activity类的类名和类成员都不混淆
    -keep public class * extends android.app.Activity{*;}
    
    # 所有继承activity类的类名和类的指定构造方法不混淆；如果该类没有匹配到此构造方法，此条指令对该类失效
    -keep public class * extends android.app.Activity{
    	public <init> (org.json.JSONObject);
    }
    ```

11. keepattributes

    ```
    不混淆范型
    -keepattributes Signature
    ```

12. dontwarn

    ```
    # 忽略com.whh.activity及其子包下的所有报警
    -dontwarn com.whh.activity.**
    ```

### android常用混淆配置

```
# 代码混淆压缩比，在0~7之间，默认为5，一般不做修改
-optimizationpasses 5

# 这个过滤器是谷歌推荐的算法，一般不做更改
-optimizations !code/simplification/cast,!field/*,!class/merging/*

# 混合时不使用大小写混合，混合后的类名为小写
-dontusemixedcaseclassnames

# 指定不去忽略非公共库的类
-dontskipnonpubliclibraryclasses

# 包含有类名->混淆后类名的映射关系
-verbose

# 指定不去忽略非公共库的类
-dontskipnonpubliclibraryclassmembers

# 不做预校验，Android不需要preverify，去掉这一步能够加快混淆速度。
-dontpreverify

# 保留Annotation不混淆
-keepattributes *Annotation*,InnerClasses

# 避免混淆泛型
-keepattributes Signature

# 抛出异常时保留代码行号
-keepattributes SourceFile,LineNumberTable

# 保留我们使用的四大组件、Application等等这些类不被混淆
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Appliction
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class com.android.vending.licensing.ILicensingService

# 保留support下的所有类及其内部成员
-keep class android.support.** {*;}

# 保留R下面的资源
-keep class **.R$* {*;}

# 保留本地native方法不被混淆
-keepclasseswithmembernames class * {
    native <methods>;
}

# 保留在Activity中的方法参数是view的方法，避免layout中引用出错（比如onClick引用）
-keepclassmembers class * extends android.app.Activity{
    public void *(android.view.View);
}

# 保留枚举类不被混淆
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

#保留我们自定义控件（继承自View）不被混淆
-keep public class * extends android.view.View{
    *** get*();
    void set*(***);
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}

# 保留Parcelable序列化类不被混淆
-keep class * implements android.os.Parcelable {*;}

# 保留Serializable序列化的类不被混淆
-keepclassmembers class * implements java.io.Serializable {*;}

# 对于带有回调函数的onXXEvent的，不能被混淆
-keepclassmembers class * {
    void *(**Event);
}

# webview 混淆规则
-keepclassmembers class fqcn.of.javascript.interface.for.webview {
   public *;
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String);
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.webView, jav.lang.String);
}
-keepclassmembers class com.ljd.example.JSInterface {
    <methods>;
}

# 所有反射的类也需要keep住
# -keep...

# 第三方依赖包
# -keep...
```



## 参考

[proguard官网](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/introduction.html)


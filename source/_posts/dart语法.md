---
title: dart语法
date: 2020-01-28 20:18:44
categories: flutter
tags:
     - flutter
description: dart语法介绍。
---

### 类定义
```
class Test{
    var test0=true;// 等同 bool test0=true;
    var test1=1;// 等同 int test1=1;
    var test2="test2";// 等同 String test2="test";
    var test3=1.1;// 等同 double test3=1.1;
}

```

### 定义枚举
```
enum Test{
    a,b,c;
}
```

### 变量定义
```
class Test{
    var test0=true;// 等同 bool test0=true;
    var test1=1;// 等同 int test1=1;
    var test2="test2";// 等同 String test2="test";
    var test3=1.1;// 等同 double test3=1.1;
    var _test4="private修饰前面加_";
}
```

### 方法定义
```
class Test{
    void function0(String str0,String str1){
        debugPrint("$str0$str1");
    }
    
    String _function0(String str0,String str1){
        return str0+str1;
    }
}
```





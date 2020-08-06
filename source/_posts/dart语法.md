---
title: dart语法
date: 2020-01-28 20:18:44
categories: flutter
tags:
     - flutter
description: dart语法介绍。
---

### dart类型（dart一切皆为Object的子类，即便是int类型，默认值null）
#### 一类
1. int
2. double（双精度，没有float类型）
3. bool
4. String
5. List
6. Map
7. Set
8. dynamic

#### 二类
1. class（类为单extends，多实现implement）
2. abstract class（支持抽象方法的类）
3. mixins（with用来复用多个类之间的代码减少以耦合，with类不能有构造方法）
4. 接口(有接口，但是没有interface关键字；普通类和抽象类都可被implement，但是被implement的类需要将其属性和方法都实现一遍)

### 构造方法
1. 普通构造函数（Test()）
2. 命名构造函数（Test.fromJson(Map json)）
3. 常量构造函数（const Test()//创建编译器常量，不能有方法体）
4. 工厂构造函数（factory Test(String type){}// 工厂模式可以返回对象）

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

### dart 异步实现
1. event queue

2. microtask queue





---
title: dart语法
date: 2020-01-28 20:18:44
categories: flutter
tags:
     - flutter
description: dart语法介绍。
---

### 类型（dart一切皆为Object的子类，即便是int类型，默认值null）
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

### 非空安全
#### 定义
```
var test = null // 不需要显示定义
```

#### 非空判断
 - 安全调用运算符（?.）
 - 强制解包（无，因为dart默认所有对象可空）
 - Elvis运算符（??）
 
### 操作符
#### ==
```
同java的==
```

#### is
```
用于对象判断
```

### 构造方法（不能重载）
1. 普通构造函数（Test()）
2. 命名构造函数（Test.fromJson(Map json)）
3. 常量构造函数（const Test()//创建编译器常量，不能有方法体）
4. 工厂构造函数（factory Test(String type){}// 工厂模式可以返回对象）

### 析构函数（无）

### 常量
1. final 运行时固定
2. const 在编译期固定

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
    // 没有访问修饰符，没有方法关键字，返回值在方法前面声明
    void _function0(String str0,String str1){
        debugPrint("$str0$str1");
    }
    
    @Override
    String _function1(String str0,String str1){
        return str0+str1;
    }
}
```

### 单例实现
```
class Test{
    static Test _instance;
    
    // 构造方法私有
    Test._();

    // 没有多线程，不存在线程安全
    static Test getInstance() {
        if (_instance == null) {
          _instance = Test._();
        }
        return _instance;
    }
}
```

### 异步实现
1. event queue

2. microtask queue

### 闭包
 




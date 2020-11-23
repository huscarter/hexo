---
title: swift语法
date: 2020-01-28 20:18:44
categories: ios
tags:
     - swift
description: swift语法介绍。
---

### 类型（swift一切皆为Object的子类，即便是int类型，默认值null）
#### 一类
1. Int
2. UInt（无符号Int）
3. Float（32位）
4. Double（64位）
5. String
6. Bool
7. Character
8. typealias（别名）
9. Optional（可选类型：有值或nil）

#### 二类
1. class（类）
1.1 可以extends
1.2 引用传递
```
class Test{
    var a:Int = 1
    let b:Int = 2
    func testFun(arg1:Int)->Bool{
        return true;
    }
}
```
2. struct（结构体）
2.1 不能继承
2.2 值传递
```
struct Test{
    var a:Int = 1
    let b:Int = 2
    func testFun(arg1:Int)->Bool{
        return true;
    }
}
```

3. 协议（protocol，即为java中的接口）
```
protocol TestP{
    var marks: Int { get set }// 声明后加上{ set get }来表示属性是可读可写的
    var result: Bool { get }
    func attendance() -> String // 不能有方法体
    init(arg:String) // 能定义构造方法
}
//
class TestC:TestP{
    var marks = 1
    var result = false
    func attendance()->String(){
        // 
    }
    required init(argument:String){ // 使用required修饰符可以保证所有的遵循该协议的子类也必须实现此构造方法
        print(argument)
    }
}
//
struct TestS:TestP{
    var marks = 1
    var result = false
    func attendance()->String(){
        // 
    }
    init(argument:String){ // 结构体不需要required关键字
        print(argument)
    }
}
```

### 非空安全
#### 定义
```
var test? = null // 不需要显示定义
```

#### 非空判断
1. 安全调用运算符（?.）
2. 强制解包（!.）
3. Elvis运算符（??）
4. if let test = arg {// 非空条件下的业务逻辑}
5. guard let test = arg {// 为空条件下的业务逻辑}

### 操作符
#### ==
```
内容相等为treu（不管内存地址）
```

#### ===
```
内存地址相等为true
```

### 构造方法
1. 非Optional属性都需要在有初始值，如果定义时没有赋值，需要构造函数里赋值。
2. 子类属性赋值之后构造函数需要调用父类构造函数。
3. 构造函数可以重载。
```
class Test : TestFather{
    var v : String
    // 构造函数
    init(){
        v = "hello"
        super.init()
    }

    // 构造函数重载
    init(arg : String){
        v = arg
        super.init()
    }
}
```

### 析构函数（OC采用ARC引用计数实现垃圾回收）
```
class Test : TestFather{
    // 析构函数无括号
    deinit{
        // 一些手动释放资源的操作
    }
}
```

### 常量
1. let 定义常量
2. static let 定义静态常量
```
class Test : TestFather{
    let v1:String = "h"
    static let v2:String = "h"
}
```

### 类定义
```
class Test{
    var test0=true;// 等同 var test0:Bool = true;
    var test1=1;// 等同 var test1:Int=1;
    var test2="test2";// 等同 var test2:String="test";
    var test3=1.1;// 等同 var test3:Float=1.1;
}
```

### 定义枚举
```
enum Test{
    case a
    case b
    case c
}
```

### 变量定义
```
class Test{
    private var test0=true;// 等同 var test0:Bool = true;
    public var test1=1;// 等同 var test1:Int=1;
    var test2="test2";// 等同 var test2:String="test";
    var test3=1.1;// 等同 var test3:Float=1.1;
}
```

### 方法定义
```
class Test{
    // 
    func function0(str0:String,str1:String){
        print("\(str0)\(str1)");
    }
    
    @Override
    String function1(String str0,String str1)->String{
        return str0+str1;
    }
}
```

### 单例实现
```
class Test {
    // 全局变量
    static let shared = Test()

    private init() {
        // 
    }
}
```

### 异步实现
1. GCD（Grand Central Dispatch）
```
let queue = DispatchQueue(label: "XXX.XXX.XXX")
queue.sync {
    // 同步业务逻辑
}
queue.async {
    // 异步业务逻辑
}
```
2. NSOperationQueue
3. NSThread
4. PThread

### 闭包（Closure，类似匿名函数、lambda表达式）
1. 全局函数	
1.1 有名字但不能捕获任何值。

2. 嵌套函数
2.1 有名字，也能捕获封闭函数内的值。

3. 闭包表达式
3.1 无名闭包，使用轻量级语法，可以根据上下文环境捕获值。



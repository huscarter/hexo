---
title: kotlin语法
date: 2020-07-28 20:18:44
categories: android
tags:
     - kotlin
description: 使用了kotlin写了一个项目，整理一下kotlin的相关知识，方便以后温习。
---

### 类(像dart一样可空，所以一切为对象)
#### 一类（摒弃类java的基础类型，都是对象类型）
1. Int
2. Long
3. short
4. Byte
5. Double
6. Float
7. Char
8. Boolean

#### 二类（同java）
1. class（类为单extends，多实现implement）
2. abstract class（支持抽象方法的类）
3. 接口

### 非空安全
#### 定义
```
var test? = null // // 不需要显示定义?
```

#### 非空判断
 - 安全调用运算符（?.）
 - 强制解包（!!.）
 - Elvis运算符（?:）
 
### 操作符
#### ==和equals
```
同java的equals
```

#### ===
```
同java的 ==
```

### 构造方法（可以重载）
1. 主构造方法
```
// 主构造方法
class Test(userId:String,userName:String){
    var userId: String? = null
    var userName: String? = null 
}
```
2. 从构造方法
```
class Test(userId:String,userName:String){
    var userId: String? = null
    var userName: String? = null
    // 从构造方法，必须调用主构造方法
    constructor(userId:String) : this(userId,"") {
        this.userId = userId
    }
}
```

### 析构函数（无）

### kotlin的静态变量
```
companion object{
    // java代码引用XXX.Companion.NAME
    const val NAME = "String name"

    // java代码引用XXX.Companion.testFun
    fun testFun(){
        // 
    }
}

// 可使用注解方便java代码引用
companion object{
    // java代码引用XXX.NAME
    @JvmField
    const val NAME = "String name"

    // java代码引用XXX.testFun
    @JvmStatic
    fun testFun(){
        // 
    }
}
```

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
// 需要在enum后面加class
enum class Test{
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
    var _test4="xxx";等同 String test3=1.1;
}
```

### 方法定义
```
class Test{
    // 需要fun关键字，需要访问修饰符，返回值写在:后面
    private fun function0(String str0,String str1):Boolean{
        return str0+str1;
    }
    
    // 重写方法override关键字大头
    override fun function1(String str0,String str1){
        Log.i(TAG,"$str0$str1");
    }
}
```

### 单例实现
```
class SSlContextProvider private constructor() {
    companion object {
        private val TAG = SSlContextProvider::class.java.simpleName
    
        val instance: SSlContextProvider by lazy(mode = LazyThreadSafetyMode.SYNCHRONIZED) {
            SSlContextProvider()
        }
    }
}
```

### 异步实现
```
// 同步实现（kotlin没有关键字synchronized和volatile，但是有synchronized注解）
@Synchronized fun test() {
  println("hello")
}

// 异步
thread(start = true) {  
  println("hello")
}
```

### Coroutines（协程）
可以像同步顺序执行一样处理异步编程。





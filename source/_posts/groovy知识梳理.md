---
title: groovy知识梳理
date: 2020-12-28 09:29:44
categories: android
tags: groovy
description: Groovy是一种基于JVM（Java虚拟机）的敏捷开发语言，它结合了Python、Ruby和Smalltalk的许多强大的特性，Groovy 代码能够与 Java 代码很好地结合，也能用于扩展现有代码。由于其运行在 JVM 上的特性，Groovy也可以使用其他非Java语言编写的库。
---

平日即便我们不动groovy也不影响我们Android开发，因为我们一般不会对build.gradle进行太多编辑。但是如果涉及项目架构调整和app优化，groovy会对你提供很大帮助，可以说是Android高阶的一个必要门槛。

## groovy的优点

1. 同时支持静态和动态类型。
2. 支持运算符重载。
3. 本地语法列表和关联数组。
4. 对正则表达式的本地支持。
5. 各种标记语言，如XML和HTML原生支持。
6. Groovy对于Java开发人员来说很简单，因为Java和Groovy的语法非常相似。
7. 您可以使用现有的Java库。
8. Groovy扩展了java.lang.Object。

## groovy的使用

### groovy环境安装（mac环境）

1. 推荐使用brew安装，命令如下：

   ```
   brew install groovy
   ```

2. 验证安装结果

   ```
   groovy --version
   ```

3. 添加环境变量

   ```
   vim ~/.bash_profile
   # 添加groovy环境变量
   GROOVY_HOME=/usr/local/opt/groovy/libexec
   ```

### IDE使用

目前推荐使用idea或者AndroidStudio进行编写。下面介绍一下AndroidStudio的groovy运行步骤，idea同理。

1. as是google专门给Android开发的同学使用的，所以首先我们创建一个Android项目。

2. groovy module和Android的application module不能同事存在，所以需要在项目单独新建一个javalib module，将此module的build.gradle改为如下样子。

   ```
   //apply plugin: 'java-library'
   apply plugin: 'groovy'
   
   dependencies {
   implementation fileTree(dir: 'libs', include: ['*.jar'])
   
   // groovy
   implementation localGroovy() // 添加groovy本地依赖，这样你才能使用groovy sdk
   }
   
   sourceCompatibility = "1.7" // 原有啥样就啥样不用变
   targetCompatibility = "1.7"
   ```

3. 在module里创建一个GroovyTest.groovy文件，然后敲入如下代码：

   ```
   import groovy.transform.PackageScope
   
   public class TestGroovy{
   		// PackageScope 为包内访问
       @PackageScope static String str = "Hello World"
   
       public static void main(String[] args){
           println(str)
       }
   }
   ```

4. 运行groovy文件

   4.1 配置 run configuration

   ![](groovy-build-0.jpg)

   4.2 增加groovy run

   ![](groovy-build-1.jpg)

   4.3 配置运行文件

   ![](groovy-build-2.jpg)

   配置完成就可以直接点击运行了！

## groovy语法

### HelloWord第一步

在自定义文件夹里创建一个HelloWorld.groovy文件，文件内容如下：

```
// 类定义
public class HelloWorld {
	// 方法定义
	public static void main(String[] args){
		// 变量定义
		String str = "Hello World";
		System.out.println(str);
	}
}
```

你没看错这个是groovy代码，不是java代码！可想而知groovy和java语法是有多像，当你忘记了groovy语法时，你可以直接使用java语法写。

### 访问修饰符

1. public（同java）

2. protected（同java）

3. private（同java）

4. <font color="red">default（不写访问修饰符）</font>

   4.1 类或者方法：default等同public

   4.2 变量：default范围在protected和private之间，只能用于本类和子类，不能夸包调用。

### 数据类型

*1. 基础数据类型

| 类型    | 取值范围                                                |
| ------- | ------------------------------------------------------- |
| byte    | -128到127                                               |
| short   | -32,768到32,767                                         |
| int     | -32,768到32,767                                         |
| long    | -9,223,372,036,854,775,808到+9,223,372,036,854,775,807  |
| float   | 1.40129846432481707e-45到3.40282346638528860e + 38      |
| doble   | 4.94065645841246544e-324d 到1.79769313486231570e + 308d |
| Boolean | true、false                                             |

*2.  <font color="red">String和GString</font>

*2.1单引号中的内容为String

*2.2 双引号中如果有使用参数则这是GString，如provide "androidx.appcompat:appcompat:$version"

*3. <font color="red">数组</font>

groovy中{}是为闭包使用的，所以你不能像java那样初始化数组。

```
// java
int[] intArr = {1,2,3};
// groovy
int[] intArr = [1,2,3];
```

*4. 其他和java类似

*5. <font color="red">类型的封装</font>

因为groovy中一切都是Object，所以它会对原始的引用进行封装，不遵循java的扩展>装箱原则。

```
void toOut(long arg){
	println("long arg");
}

void toOut(Integer arg){
	println("Integer arg");
}

int i = 2;
// java扩展>装箱，输出long arg
// groovy自动封装int为Integer，输出Integer arg
toOut(i);
```

*6. <font color="red">类型对运行时分派的影响</font>

groovy在运行时来选择调用的方法，叫运行时分派；这和java解释执行不同。看下面的例子。

```
void toOut(Object arg){
	println("Object arg");
}

void toOut(String arg){
	println("String arg");
}

Object obj = "test";
// java在编译之后生成的字节码就指定了方法的引用，输入Object arg
// groovy在运行时判断arg为String类型，输出String arg
toOut(obj);
```



### 变量的定义

Groovy中的变量可以通过两种方式定义：使用数据类型的本地语法，或者使用def关键字。

```
class Demo{
	String str = "xxx";
	def arr = [1,2,3]; // 可以不用指定变量类型使用def，groovy在运行时会自动识别
}
```

### 方法定义

Groovy中方法的定义也可以通过2种方式：使用返回类型、或者使用def关键字。

```
class demo{
	String testFun(){
		return "Hello world"
	}
	
	def testFun(String arg){
		return arg
	}
}
```

### 类的定义

同java

### 条件循环语句

同java

### 运算符

1. ==

   groovy中==相当于java的equals，如果要比较引用地址请使用is（is相当于java的==）。

2. is

   相当于java的==

3. <=>（比较运算符）

   相当于java的compareTo

## gradle常用语法



## 问题记录

1. brew 安装groovy时出现"Error: Checksum mismatch"错误。

   1.1 错误信息

   ```
   Checksum mismatch.
   Expected: b065e5e3783954f3e65d8d3a6377ca51649bfcfa21b356b0dd70490f74c6bd86
   Actual: edf2ddb225499729d3dc20c460fbc7b05d60c6e205198cbb8b51e1db7c0ff8dc
   Archive: /Users/whitewei/Library/Caches/Homebrew/portable-ruby-2.6.3_2.yosemite.bottle.tar.gz
   To retry an incomplete download, remove the file above.
   Error: Failed to install Homebrew Portable Ruby (and your system version is too old)!
   ```

   1.2 解决办法

   ```
   根据提示信息删除导致中断的文件即可。
   ```

2. xx


## 参考

### [groovy教程](https://www.w3cschool.cn/groovy/)

### [groovy官网](http://www.groovy-lang.org/)


---
title: python知识梳理
date: 2020-10-19 15:29:44
categories: 脚本语言
tags: python
description: python是一种跨平台的计算机程序设计语言。 是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。最初被设计用于编写自动化脚本(shell)，随着版本的不断更新和语言新功能的添加，越多被用于独立的、大型项目的开发。
---

因公司的weex项目使用python编写了shell脚本，为了后期维护因此学习了python。将其基础语法记录于此用于后期回顾。

## python环境安装

### windows

请移步到[python官网](https://www.python.org/downloads/)查看文档

### mac

mac电脑系统自带了python，你可以使用下面的命令查看当前的python版本。

```
python --version
```

## 运行python程序

### 交互式编程

交互式编程不需要提前编写python脚本文件，直接在命令行工具中输入运行代码按回车键即可执行。

1. 进入交互式编程

   ```
   python
   ```

2. 退出交互式编程

   ```
   按住ctr+z
   ```

### 脚本式编程

1. 编写好python文件test.py，文件内容如下

   ```
   print ("Hello, Python!")
   ```

2. 执行脚本文件

   ```
   python test.py
   ```

3. 可执行文件方式

   3.1 在test.py文件头部添加如下代码，用来指明解释器指令。

   ```
   # 注意"#"是注释符号，但是"#!"不是注释（是shebang标识符）
   #!/usr/bin/python
   ```

   3.2 在命令行中输入如下命令即可执行test.py

   ```
   ./test.py
   ```

## python语法

### 基础语法

python没有传统编程语言的{}用来指明代码块，也没有访问修饰符用来控制变量的访问权限。python有其特定的基础语法，下面来一一介绍。

1. 单下划线标识符_

   ```
   _foo # _ 代表不能直接访问的类属性，不能用 from xxx import * 而导入
   ```

2. 双下划线标识符

   ```
   __foo # 以双下划线开头代表类的私有属性
   ```

   ```
   __foo__ #	以双下划线开头和结尾的代表python里特殊的标识，比如 __init__()代表构造函数
   ```

3. 缩进（因为没有"{}"靠对称缩进管控代码）

   ```
   if True:
       print ("True") // 一般是4个空格
   else:
       print ("False") // 如果缩进和上一个没有对称执行会报错
   ```

4. 多行语句

   python语句中一般以新行作为语句的结束符，但是我们可以使用斜杠（ \）将一行的语句分为多行显示。

   ```
   # 使用\连接多行语句
   total = item_one + \
           item_two + \
           item_three
   # 在[]、()、""语句中不需要多行连接符
   days = ['Monday', 'Tuesday', 'Wednesday','Thursday', 
   				'Friday']
   ```

5. 引号' '、" "、""" """

   ```
   world0 = '这是一个属性' # 单引号
   world1 = "这也是一个属性" # 双引号
   world2 = """这还是一个属性""" # 三引号
   ```

6. 空行

   函数之间、类之间使用空行分隔。

7. 同行多条语句

   ```
   print("hello");print("world")
   ```












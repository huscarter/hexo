---
title: python知识梳理
date: 2020-10-19 15:29:44
categories: script
tags: python
description: python是一种跨平台的计算机程序设计语言。 是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。最初被设计用于编写自动化脚本(shell)，随着版本的不断更新和语言新功能的添加，越多被用于独立的、大型项目的开发。2020年1月1日，官方宣布python2停止更新，python2.7成为了pythond2的最后一个版本。
---

因公司的weex项目使用python编写了shell脚本，为了后期维护因此开始了python的学习，将其基础语法记录于此用于后期回顾。

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
   _foo # _ 代表不能直接访问的类属性(类似protected)，不能用 from xxx import * 而导入
   ```

2. 双下划线标识符

   ```
   __foo # 以双下划线开头代表类的私有属性(类似private)
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

5. 引号'' 、""、 """"""

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

8. 中文支持

   python使用ASCII为默认编码格式，输出中文会报错，可以在文件头部添加下面的代码来解决。

   ```
   # coding=utf-8
   # 或者
   # -*- coding: UTF-8 -*-
   ```

9. 注释

   ```
   # 这是一个单行注释
   
   '''
   这是一个多行注释
   这是一个多行注释
   '''
   
   """
   这也是一个多行注释
   这也是一个多行注释
   """
   ```

### 标准数据类型

   1. Number数字

      1.1 int

      ```
      # 不需定义访问修饰符和类型
      test0 = 1
      test1 = -1
      ```

      1.2 long（长整型，可代码八进制和十六进制）

      ```
      test0 = 10241024L
      test1 = -0x1928FTT
      ```

      1.3 float

      ```
      test0 = 1.2
      test1 = -0.9
      ```

      1.4 complex（复数）

      ```
      test0 = 3e+26J
      test1 = 3.14j
      ```

   2. String字符串

      ```
      test = "abcdefg"
      print test[1:5] # bcdef
      ```

   3. List列表

      ```
      test = [1,2,"a","b","c","3"]
      print test # [1,2,"a","b","c","3"]
      print test[1:] # [2,"a","b","c","3"]
      print test *2 # [1,2,"a","b","c","3",1,2,"a","b","c","3"]
      print test + test # [1,2,"a","b","c","3",1,2,"a","b","c","3"]
      ```

   4. Tuple元组

      元组可以说是不能二次赋值的数组

      ```
      test = (1,2,"a","b","c","3")
      test[1] = 3 # 非法，元组不允许修改
      print test # (1,2,"a","b","c","3")
      print test[1:] # (2,"a","b","c","3")
      print test *2 # (1,2,"a","b","c","3",1,2,"a","b","c","3")
      print test + test # (1,2,"a","b","c","3",1,2,"a","b","c","3")
      ```

   5. Dictionary字典

      同数组一样灵活，区别是字典通过键值对获取，列表通过下标获取。

      ```
      test = {"key0":"value0","key1":"value1","key1":"value1"}
      print test["key0"] # value0
      print test # {"key0":"value0","key1":"value1","key1":"value1"}
      print test.keys # ["key0","key1","key2"]
      print test.values # ["value0","value1","value"]
      ```

### 常用语句

   1. 条件语句

      1.1 if

      ```
      # 单判断
      test = 1
      if test == 0:
      	print "等于0"
      elif test == 1:
      	print "等于1"
      else:
      	print "等于其他"
      	
      # 多判断
      test = 1
      if test >0 and test<3:
      	print("0<test<3")
      elif not test == 4:
      	print("test not 4")
      ```

      1.2 <font color = "red">python不支持switch</font>

   2. 循环语句

      2.1 while

      ```
      test = 0
      while test<10
      	print "test 等于:",test
       	test++
      ```

      2.2 for

      ```
      test = ["a","b","c"]
      for w in test
      	print "当前数组内容为:",w
      	
      for index in range(len(test))
      	print "当前数组内容为:",test[index]
      ```

   3. pass 语句

      ```
      # 定义一个空方法，里面需要使用pass语句，否则会报错
      def sample(test):
          pass
      ```

### 面向对象编程
   1. python 是一门面向对象语言，支持类的继承、方法重载。

   2. <font color="red">类中的每个方法第一个参数必须是self。</font>

   3. 子类重载的方法父类的该方法将不会再调用，如要调用子类可以通过super手动调用。

   4. 通过from xxx import Xxx来引入一个类。

   5. <font color="red">如果建立了包目录，需要在包下建立__init__.py文件（可空）用来指明此目录为包目录</font>

      father.py 文件

      ```
      # coding=utf-8
      
      # Father 类
      class Father(object):
        def __init__(self,name,age,sex):
          self.name = name
          self.age = age
          self.sex = sex
      
        def show(self):
        	print "name: %s,age:%s, sex:%d" % (self.name,self.age,self.sex)
      ```

      son.py 文件

      ```
      # coding=utf-8
      from father import Father
      
      # Son 类
      class User(Father):
        def __init__(self,name,age,sex,score):
          Father.__init__(self,name,age,sex)
          self.score = score
      
        def show(self):
          super(self)
          print "score:",self.score
      ```

      test.py文件

      ```
      #!/usr/bin/python
      # coding=utf-8
      from son import Son
      
      son = Son("whh",29,1,90)
      son.show()
      ```



## 常用api

### 日期时间

1. 耗时实现

   ```
   import time
   
   startTime = time.time() # 1606467920.91 当前时间合成的秒数
   # did some thing
   print "cost time:%s" %(time.time()-startTime)
   ```

### OS模块常用api

1. 路径拼接

   ```
   import os
   
   path0 = "/src/page"
   path1 = "test.py"
   print os.path.join(path0,path1) # /src/page/test.py
   ```

2. 获取文件所在的目录

   ```
   import os
   
   file_path = "../src/page/test.py"
   print os.path.dirname(file_path) # ../src/page/
   ```

3. 存在文件

   ```
   os.path.exists
   ```



### SET

set集合不允许有重复的值

1. 添加

   ```
   setTest = set()
   setTest.add(obj)
   ```

2. 删除

   ```
   setTest = set()
   setTest.add(obj)
   setTest.remove(obj)
   ```

3. 获取

   ```
   setTest = set()
   setTest.add(obj)
   for obj in setTest
   	print ("obj:",obj)
   ```

4. 取2个set的差集

   <font color="red">主要需要定义新的变量来接收返回值</font>

   ```
   x = {"apple", "banana", "cherry"}
   y = {"google", "microsoft", "apple"}
   # 返回一个集合，元素包含在集合 x ，但不在集合 y 
   z = x.difference(y) 
   print(z) # {'cherry', 'banana'}
   ```

5. 2个set取合集

   <font color="red">主要需要定义新的变量来接收返回值</font>

   ```
   x = {"apple", "banana", "cherry"}
   y = {"google", "microsoft", "apple"}
   z = x.union(y)
   print(z) # {"apple","banana","cherry","google","microsoft"}
   ```

   

### 字典常用api

1. 匹配元素是否在dic

   ```
   dic = {"key0":"value0","key1":"value1","key2":"value2"}
   if "key0" in dic:
   	print "contain"
   else:
   	print "not contain"
   # >>> contain
   ```

2. 更新dic

   ```
   dic = {'one': 1, 'two': 2, 'three': 3}
   dic.update({'one':4.5, 'four': 9.3}) # update只影响dic
   print(a) # {'one': 4.5, 'two': 2, 'three': 3, 'four': 9.3}
   ```

   

## 问题记录

### import 第三方模块识别不了

1. 原因

   ```
   """
   import oss2 为例
   你的系统中有python2和python3两个版本
   可能你的项目ide使用的是python3，但是通过pip安装的oss库，而pip只为python2服务
   """
   ```

2. 解决

   ```
   """
   1. 可以将你的ide python版本更换为python2
   2. 或者通过pip3 安装oss
   """
   ```

   

## 参考
[python官网文档](https://docs.python.org/zh-cn/3/)
[RUNoob.com](https://www.runoob.com/python/python-tutorial.html)

   








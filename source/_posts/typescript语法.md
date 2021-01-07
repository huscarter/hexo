---
title: typescript语法
date: 2020-10-19 10:29:44
categories: script
tags: typescript

description: typescript 是微软开发的 javascript 的超集，typescript 兼容 javascript，可以载入javascript 代码然后运行。可将javascript代码以面向对象方式编程的技术，可方便大型web项目开发。
---

因公司项目中有使用 typescript，虽然项目里将其转为了js，但是怎么能停下学习的脚步呢？搞起来！此博文用来总结学习成果和方便后期回顾。总的来说typescript语法类似java和kotlin的结合体，如果你还不熟悉js当然是先去学好js再考虑接触typescript，毕竟它只是js的一个超集。

### typescript安装

1. 安装ts

   ```
   npm install -g typescript
   ```

2. 运行ts命令行

   ```
   tsc -h
   ```

3. IDE

   ```
   推荐使用visual studio code
   ```

   

### 基本数据类型

1. boolean

   ```
   var isDone:boolean = true;
   ```


2. nunber

   ```
   var width:number = 100;
   ```
   
3. string

   ```
   va name:string = "hello";
   ```
   
4. Array<T>

   ```
   var arr:number[] = [1,2,3];
   var arr:Array<number> = [1,2,3];
   ```

5. enum

   ```
   enum Color {Red,Green,Blue}
   ```
   
6. any（不确定类型）

   ```
   // 不确定简单变量类型
   var anyField:any = "hello"; 
   notSure = true;
   // 不确定数组类型
   var anyArr = [1,"hello",true];
   anyArr[0] = "world";
   ```
   
7. void

   ```
   function test():void{
   	console.log("hello world");
   }
   ```

### 可选属性

```
interface ICar {
	color?:string; // 实现类无需实现
	type?:any; // 实现类无需实现
}
```

### 类

1. 类的定义

   ```
   /*
    * 类的定义
    */
   class User {
   	// 普通属性
   	private name:string;
   	// 静态属性
   	public static age:number = 30;
   	// 构造方法
   	constructor(name:string){
   		this.name = name;
   	}
   	// 普通方法
   	public say():void{
   		// 
   	}
   }
   /*
    * 类的使用
    */
   var user:User = new User("whh");
   user.say();
   ```

2. 类的继承

   ```
   class User {}
   class Student extends User{
   	public say():void{
   		// 
   	}
   }
   ```

3. Get\Set 访问器

   ```
   class User {
   	private name:string;
   	//
   	get getName():string{
   		return this.name;
   	}
   	//
   	set setName(name:string):viod{
   		this.name = name;
   	}
   }
   ```

### 接口

1. 接口定义

   ```
   interface ICar {
   	var color:string;
   	run();
   	getType:()=>string
   }
   ```

2. 接口的实现

   ```
   class Bus implements ICar {
   	color:string;
   	getType:()=>"Bus";
   }
   ```

### 方法重载

方法的重载只和方法名称和参数有关系，和返回类型无关。

```
class User {
	public run():string{
		//
	}
	
	public run(name:string):number{
		//...
		return 10;
	}
	
	public run(age:number):number{
		//...
		return 14;
	}	
}
```

### typescript文件编译

```
tsc [目标文件] [输出文件]
```



### 参考

1. [typescript官网](http://www.typescriptlang.org/)
2. [typescript源码](http://typescript.codeplex.com/)


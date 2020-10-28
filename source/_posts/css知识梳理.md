---
title: css知识梳理
date: 2020-10-28 20:29:44
categories: web
tags: css
description: 梳理的日常的css样式知识。css里面很多属性你如果不了解就去使用会让你丈二和尚不到到头脑，这blog整理了平时开发用的css和记录了可能使用出错的css，你值得拥有。
---

## css的盒子模型
首先我们需要了解css的盒子模型，以便知道其作用域和影响效果。因为在css中一个视图所包含的内容就是css盒子模型的内容。
![css盒子模型](css盒子模型.jpeg)

### margin

margin是外边距，当前组建border和父控件之间的间隔。margin 没有背景颜色，是完全透明的。

1. margin-left

2. margin-right

3. margin-top

4. margin-bottom

   ```
   .box {
   	margin-left: 10px; // 设置左边距
   	margin-right: 11px; // 设置右边距
   	margin-top: 12px; // 设置上边距
   	margin-bottom: 13px; // 设置下边距
   }
   //等同
   .box {
   	margin: 12px 11px 13px 10px;// (top right bottom left)
   }
   ```

### border

组建的边框。

1. border-top

2. border-right

3. border-bottom

4. border-left

   ```
   .box {
   	border-left: 10px solid #eeeeee; // 设置左边框
   	border-right: 11px solid #eeeeee; // 设置右边框
   	border-top: 12px solid #eeeeee; // 设置上边框
   	border-bottom: 13px solid #eeeeee; // 设置下边框
   }
   ```

   

### padding

padding是内边距，padding是包含在组建内部的，受background影响。

1. padding-left

2. padding-right

3. padding-top

4. padding-bottom

   ```
   .box {
   	padding-left: 10px; // 设置左边内间距
   	padding-right: 11px; // 设置右边内间距
   	padding-top: 12px; // 设置上边内间距
   	padding-bottom: 13px; // 设置下边内间距
   }
   //等同
   .box {
   	padding: 12px 11px 13px 10px;// (top right bottom left)
   }
   ```

### content

内容区域，一般是文字、图片之类的。css样式可以设置它们的大小、颜色、背景等。

## 元素分类

### 行内元素（inline）

行内元素顾名思义就是在一行之内展示的元素，它不带换行，其大小仅仅被动的依赖于自身内容的大小。

1. 组建介绍

   ```
   <span>
   <a>
   <br>
   <b>
   <strong>
   <img>
   <input>
   <textarea>
   <select>
   <sup>
   <sub>
   <em>
   <del>
   ```

2. <font color="red">行内元素特性</font>

   2.1 不能设置高、宽以及行高（大小由内容决定）。

   2.2 水平方向的padding和margin属性可以设置，但是垂直方向上的无效。

   2.3 总是和相邻的行内元素在同一行上。

   2.4 行内元素只能容纳其他行内元素。

### 块级元素（block）

块级元素会独占一行，多个块级元素会另起一行。

1. 组建介绍

   ```
   <div>
   <form>
   <table>
   <h1>~<h6>
   <hr>
   <p>
   <ul>
   <ol>
   <address>
   <center>
   <pre>
   <dl>
   ```

2. <font color="red">块级元素特性</font>

   2.1 总是另起一行。

   2.2 可以设置其高度、宽度、行高以及内外边距。

   2.3 在不手动设置宽度的情况下，宽度默认为所在容器的100%。

   2.4 可以容纳行内元素和其他块元素。

### 行内块元素（inline-block）

一种特殊的行内元素，它综合了行内和块级元素的特性。

1. 组建介绍

   ```
   <img>
   <input>
   <td>
   ```

2. <font color="red">行内块元素特性</font>

   2.1 和相邻的元素在同一行，但是之间会有空白间隙。

   2.2 默认的宽度是它们内容本身。

   2.3 宽度、高度、行高、外边距和内边距都可以设置。

## css的单位

### 相对长度

1. em

   参考物是父元素的font-size，具有继承的特点。如果父元素没有设置使用浏览器默认的16px。

   ```
   font-size: 1em; // 1em == 16px;2em == 32px;
   ```

2. rem

   rem是相对于根元素html，我们只需要在根元素确定一个参考值，比如大小为10px，到时设置1.2rem就是12px，以此类推。

3. vw

   css3新单位，view width的简写。假如可视屏幕宽度是1200px的话，那100vw就是1200px。

4. vh

   css3新单位，view height的简写。假如可视屏幕高度是1200px的话，那100vh就是1200px。

5. %

   这个就是按照父元素的百分比来设置大小。

6. rpx

   小程序的单位，可以根据屏幕宽度进行自适应。

   ```
   // 规定屏幕宽为750rpx。iPhone6屏幕宽度为375px，共有750个物理像素。则750rpx = 375px = 750物理像素
   width: 1rpx; //1rpx == 0.5px == 1物理像素
   ```

### 绝对长度

1. px

   像素

2. cm
   厘米

3. mm

   毫米

4. pt

   point（1pt = 1/72英寸，1in = 96px = 2.54cm）

5. pc

   pica，大约6pt

## 选择器

### 类选择器

通过元素的class选择，返回值是一个数组。

```
.class
```

### id选择器

通过元素的id选择，返回值是单个元素

```
#id
```

### 元素选择器

```
div // 选择所有div元素
div p // 选择所有div下的p元素
```

### ：选择器

泛指一系列的属性、状态选择器

```
:link // 选择所有未访问链接
:visited // 选择所有访问过的链接
:active // 选择活动链接
:hover // 选择鼠标在链接上面时

:focus // 选择具有焦点的输入元素

:before //如 p:before 意思是在每个<p>元素之前
:after // 如 p:after 在每个<p>元素之后
```

## css 常用属性

### margin、padding

在讲盒子模型时说过了，且记住它们在行内元素和块级元素的不同效果。

### border

1. border-top、border-right、border-bottom、border-left

   ```
   // 设置border的上部分的宽度、颜色
   border-top: 1px solid #eeeeee; // 需要设置solid，否则默认是none此时1px不起作用
   ```

2. borde-width、border-top-width

   设置边框的尺寸

3. border-color、border-top-color

   设置边框的颜色

4. border-raduis、border-top-raduis

   设置边框的圆角幅度

5. border-style

   设置边框的样式

   ```
   border-style: none; // 默认，无边框
   border-style: hidden; // 同none，但是对于表用于解决边框冲突
   border-style: dotted; // 点状
   border-style: solid; // 实线
   border-style: double; // 双线
   border-style: dashed; // 虚线
   ```

### box

1. box-shadow

   ```
   box-shadow: 10px 10px 5px #888888; // 向右偏移10px，向下偏移10px，阴影部分5px
   ```

2. <font color="red">box-sizing</font>

   ```
   box-sizing: border-box; // 对元素指定宽度和高度包括了 padding 和 border（为 IE 模型）
   box-sizing: content-box; // 元素的填充和边框布局和绘制指定宽度和高度除外（为 W3C 标准模型）
   ```

### display

### float

### flex

### z-index

## 参考

### [runoob](https://www.runoob.com/cssref/css-reference.html)


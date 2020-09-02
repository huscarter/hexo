---
title: android项目开发规范
date: 2016-07-12 06:56:04
categories: android
tags: 
      - android
description: 无规矩不成方圆。
---

## 命名
### 项目命名
pascal，首字母一律大写。（如：TradingCoupon）
不推荐后面加版本号1.1.x（如：Trading1.1.6），如果线上版本出现了升级，升级后的版本可能会与当前的版本冲突；但是可以加第几期（如：TradingCoupon2）。

### 包命名
一律小写，格式为“com.公司.项目名.模块名”。(如：com.jushi.trading.bean)

### 变量命名
1. 静态变量：一律大写，字母之间用“_”分开。（如：SHARE_NAME_KEY）
2. 一般变量：字母之间用“_”分开。(如：user_name)

### 方法命名
驼峰法camel，第一个首字母小写，剩下的首字母一律大写。（如：getOrderDetail(String order_id)）

### 文件命名
1. java类命名（非接口）
1.1 pascal，首字母一律大写。如：UserAddress.java）
1.2 activity类，后面需要添加Activity。（如：UserAddressActivity.java）
1.3 adapter类，后面需要添加Adapter。（如：UserAddressAdapter.java）
1.4 fragment类，后面需要添加Fragment。（如：UserAddressFragment.java）
1.5 bean，后面不加Bean。（如：User .java）
1.6 Uitl类，需要添加Util。（如DateUtil.java）
1.7 数据库操作文件，后面要加Dao。（如：UserDao. java）

2. java类（接口）
2.1 普通的借口类，命名为IXxx.java。（如IuserRequest.java）
2.2 Listener回调之类的用OnXxLisenter形式。（如 OnClickListener.java）
2.3 其他的自定义，但是必须是pascal命名格式。

3. 布局文件
3.1 Activity布局文件:一律按activity_xx.xml的形式。（如actvity_user_address.xml）
3.2 Adapter布局文件:一律按item_xx.xml的形式。 (如item_user_address.xml)
3.3 include 布局文件:一律以include_xx.xml的形式。（如：include_user_address.xml）

4. 图片(一律按xx_xx_xx.png的形式)
4.1 图标：icon_user_address.png
4.2 按钮：
4.2.1 btn_commt.png（正常状态）
4.2.2 btn_commt_un.png（点击时的状态）
4.3 背景：bg_login.png

5. Drawable描述文件
5.1 带有点击效果的selector
5.1.1 selector_corner_orange.xml（圆角橙色）
5.1.2 selector_rectangle_orange.xml（直角橙色）
5.2 带有check属性值selector
5.2.1 chck_corner_orange.xml（圆角橙色）
5.2.2 chck_rectangle_orange.xml（直角橙色）
5.3 圆角有填充色
5.3.1 shape_corner_orange.xml
5.4 圆角有填充色有边框
5.4.1 shape_corner_border_orange.xml
5.5 圆角只有边框图
5.5.1 stroke_corner_orange.xml
5.6 直角只有边框图
5.6.1 stroke_rectangle_orange.xml

### android控件id命名
控件:缩写
1. RelativeLayout:rl_
2. LinearLayout:ll_
3. TextView:tv_
4. EditText:et_
5. ImageView:iv_
6. Buttom:btn_
7. CheckBox:cb_
8. RadioButton:rb_
9. RadioGroup:rg_
10. TimerPicker:tp_
11. ProgressBar:pb_
12. ScrollView:sv_
13. SimpleDraweeView:sdv_
14. include:i_
15. FrameLayout:fl_
16. TableLayout:tl_
17. GridLayout:gl_
18. Switch:s_

## 代码
### 类中的属性、方法和内部类声明需要区分放置
1. 类的属性一律放到类的顶部。
2. 类的方法声明放在属性部分下面。
3. 内部类放在方法部分下面。
	
### 创建基类，复用基础类中的公共方法
1. 创建BaseActivity基础类，定义通用的属性如Activity activity、Context context，CompositeSubscription subscription等；定义通用的方法抽象initView(){只做组件初始化},setListener(){只做事件添加},initData(){只做数据初始化}。
2. 创建Base Bean基础类，实现Serializable，公用String status_code，String message等属性。
3. 创建BaseFragment基础类，定义通用的属性如Activity activity、Context context，CompositeSubscription subscription等。定义通用的方法抽象initView(){组件初始化},setListener(){事件添加},initData(){数据初始化}。

### 每个方法只做一件事，保持方法简洁明了
不要将控件初始化和网络请求的代码写在同一个方法里。

### 实体类规则
1. 除了类似RecycleView.ViewHolder的实体类可以是public修饰和不加getter and setter，其余的实体类都必须按照JavaBean 规范编写（private 修饰，通过get和set方法访问）。
2. 实体类统一放至bean包下，否则代码混淆后运行会报错。

### 对于Activity之间传递数据推荐使用Bundle携带数据
Bundle可以在多个Activity之间重用，不需要重新装载数据。

### 展示实体类数据格式需要修改的，统一在实体类的get方法中做格式转换
比如订单（order）总价（total_price）保留2位小数，getTotalPrice(){返回格式为小数点2位}，这样的做法是只改一处，作用于整个项目。

### 网络请求Retrofit的Obsever必须基于同一个自定义的Obsever

### 定义统一的加载组件

### 定义统一的dialog组件

### 定义统一的WebView组件以及WebViewJsBridge交互类

### 所有组件显示的字符串都必须引用于string.xml

### 不推荐使用lambda表达式
因为现在的lambda不稳定，会造成项目崩溃。

### 不要使用幽灵字符

```
if(notice_type==10011){  // 10011幽灵
	// 
} else if(notice_type==10013){ // 10013幽灵
	// 
}
```

## android资源文件定义
### 统一按钮高度
```
btn_height_max=56d
btn_height_big=48dp
btn_height_mid=32d
btn_height_sma=24dp
```

### 定义title组件的高度
```
title_height=56dp
```

### 定义条目的高度
每一个TextView,EditView等条目的高度。
```
item_height=48p
```

### 定义系统左右边距的宽度
```
app_pad=8dp
```

### 定义系统条目之间的间距
```
app_margin＝16p
```

### 定义字体大小,推荐使用dp
```
font_size_max_big=32dp
font_size_max=24dp
font_size_big=17dp
font_size_mid=15dp
font_size_sma=11dp
font_size_sma_sma=9dp
```

### 定义drawable和内容的间距
```
drawable_pad=8dp
```

### 定义圆角弧度
```
radius＝5dp
```

### 统一item图片的高宽
```
img_width_big=100d
img_width_mid=80dp
img_width_sma=56dp

img_height_big=100dp
img_height_mid=80dp
img_height_sma=56dp
```

### 定义EditView的padding
```
edit_pad=3dp
```

### 定义系统的基本色值
```
app_color=
```

### 定义字体颜色。
```
text_color_black
text_color_gray
text_color_orange
text_color_red
```

### strings.xml 使用注释区分activity


## 版本控制
### 定义gradle.properties统一管理项目中的版本信息

### 项目中创建版本说明文件CHANGELOG.md，纪录每一个版本的更新内容和时间

### 版本服务器中创建branches和tags用来存放分支和历史版本

## 注释
### 每个类头部必须编写javadoc
```
/**
 * Create by weihuihui@meishubao.com on ${DATE}
 * <p>
 * 类说明:<br/>
 * 接受传值:<br/>
 * 对外传值:<br/>
 * @since 
 */
```

### public 修饰的变量必须编写javadoc

### 方法最好都写上注释（重要逻辑方法必须写）

```
 /**
  * @author 作者
  * @version 版本
  * @see 参考转向
  * @param 参数
  * @return 返回值
  * @exception 抛出说明
  */
```

## 项目结构（以包为例）

1. UI层
1.1 activity
1.2 adapter
1.3 view（自定义view）
1.4 fragment
2. 业务逻辑层
2.1 网络请求
2.1.1 net
3. 数据库访问
3.1 dao
4. utility
4.1 DateFormat.java
4.2 Bitmap.java
4.3 Notification.java
4.4 Shared Preference.java
4.5 Environment.java
4.6 CommonUtil.java

3. data层
3.1 bean
3.2 provider
4. service
5. receiver
6. widget


---
title: android面试题整理
date: 2019-08-01 01:01:01
categories: android
tags:
     - 面试
description: 磨刀不误砍柴工，不打无准备的仗。
---

### android
1. Context 理解
    - Context--ContextImpl
             --ContextWrapper--ContextThemeWrapper
                             --Application
                             --Service

    - Context是android运行程序的上下文，通过Context可以启动activity、service和发送广播，获取资源文件，获取String定义字符，弹出Toast、Dialog等

2. 描述android的热修复
Android热修复分为java代码修复、资源文件修复和动态链接库修复。 
2.1 java代码修复：
    - 设计ClassLoader【PathClassLoader（在Dalvik中加载安装好的class）、DexClassLoader（在Dalvik和ART中可以记载未安装的dex中的类）】，所以java代码修复选中DexClassLoader。
    - DexClassLoader中有DexPathList类dexPathList有DexElements数组（每个元素就是一个dex文件），android运行是查找dexElements元素有就返回，热修复需要将新的dex插入到数组头部实现替换。    
2.2 资源文件修复
    - Activity加载资源使用了ContextImpl，ContextImpl有loadResource方法，loadResource方法中通过反射调用了AssetsManager的addAssetsPath方法将资源加载到Resource对象中。
2.3 动态链接库修复
    - 暂无
    
3. kotlin有哪些有点，用kotlin写一个Adapter基类
3.1 优点
    - 兼容java
    - 简洁 语法简洁
    - 安全 kotlin对null有安全支持，编译时就能察觉到NullPointException

3.2 Adapter基类
```
abstract class MyBaseAdapter<T>(val ctx: Context, val layoutRes: Int, val list: List<T>): RecyclerView.Adapter<MyBaseAdapter.BaseHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, position: Int): BaseHolder {
        return BaseHolder(LayoutInflater.from(ctx).inflate(layoutRes, parent, false));
    }

    override fun onBindViewHolder(holder: BaseHolder, position: Int) {
        convert(holder,position);
    }

    override fun getItemCount(): Int {
        return list.size;
    }

    abstract fun convert(holder: BaseHolder,position:Int);

    class BaseHolder(itemView: View): RecyclerView.ViewHolder(itemView){
        public val views= SparseArrayCompat<View>();

        fun <V : View> getView(id: Int): V {
            var view = views[id]
            if (view == null) {
                view = itemView.findViewById(id)
                views.put(id, view)
            }
            return view as V
        }

    }
}
```
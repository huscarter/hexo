---
title: ARoute知识梳理
date: 2020-09-12 20:29:44
categories: android
tags:
     - android
description: 梳理了对于我们开发经常使用的ARoute的工作原理。
---
ARoute是阿里开源的一个组件化路由方案，[github地址](https://github.com/alibaba/ARouter)。本文主要介绍ARoute的路由管理和其使用的APT技术。

## ARoute路由管理
ARouter通过Apt技术，生成路由路径（path）和被注解（@Router）的组件类的映射关系的类，根据用户请求（postcard）查找到目标地址（class），再通过Intent跳转。
为了避免一次性将多有的路由加载到内存，ARoute采用了分组和懒加载机制。

### 分组
通过path定义（比如/main/index），注解经过APT处理有会生成ARoute$$Group$$App和ARoute$$Group$$Main两个java文件。
1. ARoute$$Group$$app
此类由一个Map<String,Class<? extends IRouteGroup>> 比如routes记录了组名和对应组路由类的关系。
```
routes.put("main", ARouter$$Group$$main.class)
```

2. ARoute$$Group$$main
此类由一个Map<String,RouteMeta> atlas 记录了路由path和对应class的关系。
```
atlas.put("/main/fa/leakscan", RouteMeta.build(RouteType.ACTIVITY, MainFaLeakScanActivity.class, "/main/fa/leakscan", "main", }}, -1, 1));
atlas.put("/main/login", RouteMeta.build(RouteType.ACTIVITY, LoginActivity.class, "/main/login", "main", null, -1, -2147483648));
```

### 懒加载
程序一开始路由表中是没有数据的，只有当请求发生时ARoute才会去加载相应组的路由表，并将改组的路由信息缓存到Warehouse.routes中。

## APT（Android Processing Tool）


参考:
[ARoute解析](https://www.cnblogs.com/jymblog/p/11698914.html)
[云栖社区ARoute介绍](https://developer.aliyun.com/article/71687)



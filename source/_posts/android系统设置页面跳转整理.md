---
title: android系统设置页面跳转整理
date: 2020-05-08 20:29:44
categories: android
tags:
     - android
description: 有些时候我们做android开发需要申请一些系统权限，但是目前很多权限是需要用户手动设置；为了方便用户找到权限的设置页，我们可以给用户直接调起对应权限的设置页。由于国内各大品牌机型定制的原因，同一权限在不同品牌的不同机型对应的页面有可能不一样，所以有必要整理一份文档方便后期查看。
---

### 机型判别
因为权限设置页面需要根据机型来做不同处理，所以我们首先需要判断用户当前使用的机型。

1. 获取手机机型
```
String brand = Build.BRAND;
```

2. 机型判别
```
// 华为
bool isHW = brand.toLowerCase().equals("huawei")||brand.toLowerCase().equals("honor");

// 小米
bool isMI = brand.toLowerCase().equals("xiaomi")||brand.toLowerCase().equals("redmi")||brand.toLowerCase().equals("mi");

// VIVO
bool isVIVO = brand.toLowerCase().equals("vivo");

// OPPO（应为目前onePlus和realMe手机为oppo体系，所以归为一类）
bool isOPPO = brand.toLowerCase().equals("oppo")||brand.toLowerCase().equals("oneplus")||brand.toLowerCase().equals("realme");

// 魅族
bool isMZ = brand.toLowerCase().equals("meizu");
```

### 通知权限设置页
国内Android手机推送是一大痛点，别特是Android10对后台进程管控严格之后。如果要保证推送的稳定性最好是接各厂家自己的推送，统一推送联盟有在搞推必达，但是啥时候能用还不知道，蛋疼。

#### 判断通知权限是否开启
```
boolean enable = NotificationManagerCompat.from(context).areNotificationsEnabled();
```

1. 华为机型
一般情况下是跳转到google api到通知设置页，但是此页面的左面角标开关其实是无效的，因为华为自己实现了一套。如果要调转到有效的左面角标通知设置页面需要跳转到管理应用通知列表页，再找到自己的app进入华为自己的通知设置页才行；管理应用通知列表页对应的页面标识为com.huawei.systemmanager/com.huawei.notificationmanager.ui.NotificationManagmentActivity。
```
// Settings.ACTION_APP_NOTIFICATION_SETTINGS是在api 26 之后才提供的，所以需要版本判断
// 当版本低于api 26 替代方案是调转到应用详情设置页
 if (android.os.Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { // 26 -> 8.0
    Intent intent = new Intent();
    intent.setAction(Settings.ACTION_APP_NOTIFICATION_SETTINGS);
    intent.putExtra(Settings.EXTRA_APP_PACKAGE, activity.getPackageName());
    intent.putExtra(Settings.EXTRA_CHANNEL_ID, activity.getApplicationInfo().uid);
    activity.startActivity(intent);
} else {
    startSetting(result);
}

// 应用详情设置页
Intent intent = new Intent();
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.GINGERBREAD) { // 9 -> 2.3
    intent.setAction("android.settings.APPLICATION_DETAILS_SETTINGS");
    intent.addCategory(Intent.CATEGORY_DEFAULT);
    intent.setData(Uri.fromParts("package", activity.getPackageName(), null));
    context.startActivity(intent);
} else {
    Toast.makeText(context, "当前系统版本太低", Toast.LENGTH_SHORT).show();
}
```

2. 小米机型
```
同华为机型
```

3. VIVO机型
```
同华为机型
```

4. OPPO机型
```
同华为机型
```

5. 魅族机型
```
同华为机型
```

### 后台运行设置页
是否已开启后台运行权限无法检测。

1. 华为机型
- 华为设备都是通过内置应用手机管家实现app的后台运行和自启动权限管理，因此我们需要调起手机管家的主页面（com.huawei.systemmanager.mainscreen.MainScreenActivity）。
- 在较老的版本是能直接调起手机管家的应用启动管理列表页的（com.huawei.systemmanager.appcontrol.activity.StartupAppControlActivity），但是在android10的华为手机上测试报没有权限，所以保险起见统一调起手机管家首页。
- 看其他技术文件有推荐使用（com.huawei.systemmanager.optimize.process.ProtectActivity）结果发现在华为手机Android8.1的系统上运行出错。
```
intent.setComponent(new ComponentName("com.huawei.systemmanager", "com.huawei.systemmanager.mainscreen.MainScreenActivity"));
startActivity(intent);
```

2. 小米机型
```
intent.setAction("miui.intent.action.HIDDEN_APPS_CONFIG_ACTIVITY");
intent.putExtra("package_name", activity.getPackageName());
intent.putExtra("package_label", com.lianlianlink.linktalk.R.string.app_name);
intent.putExtra(Settings.EXTRA_CHANNEL_ID, activity.getApplicationInfo().uid);
startActivity(intent);
```

3. VIVO机型
Vivo老版本之前是可以直接跳转到电池高消耗应用列表页的（com.vivo.applicationbehaviorengine.ui.ExcessivePowerManagerActivity），但是在新版本Android9上跳该页面会报无权限，所以统一跳转到电池优化管理页。
```
ComponentName cn = new ComponentName("com.iqoo.powersaving", "com.iqoo.powersaving.PowerSavingManagerActivity");
intent.setComponent(cn);
startActivity(intent);
```

4. OPPO机型
```
ComponentName cn = new ComponentName("com.coloros.oppoguardelf", "com.coloros.powermanager.fuelgaue.PowerUsageModelActivity");
intent.setComponent(cn);
startActivity(intent);
```

5. 魅族机型
魅族同华为一样需要跳转到手机管家页面
```
Intent intent = getPackageManager().getLaunchIntentForPackage("com.meizu.safe");
startActivity(intent);
```

### 自运行设置页
是否已开启自运行权限无法检测。

1. 华为机型
- 华为设备都是通过内置应用手机管家实现app的后台运行和自启动权限管理，因此我们需要调起手机管家的主页面（com.huawei.systemmanager.mainscreen.MainScreenActivity）。
- 在较老的版本是能直接调起手机管家的应用启动管理列表页的（com.huawei.systemmanager.appcontrol.activity.StartupAppControlActivity），但是在android10的华为手机上测试报没有权限，所以保险起见统一调起手机管家首页。
- 看其他技术文件有推荐使用（com.huawei.systemmanager.optimize.process.ProtectActivity）结果发现在华为手机Android8.1的系统上运行出错。
```
intent.setComponent(new ComponentName("com.huawei.systemmanager", "com.huawei.systemmanager.mainscreen.MainScreenActivity"));
startActivity(intent);
```

2. 小米机型
```
ComponentName cn = new ComponentName("com.miui.securitycenter", "com.miui.permcenter.autostart.AutoStartManagementActivity");
intent.setComponent(cn);
activity.startActivity(intent);
```

3. VIVO机型
```
ComponentName cm = new ComponentName("com.vivo.permissionmanager", "com.vivo.permissionmanager.activity.BgStartUpManagerActivity");
intent.setComponent(cm);
startActivity(intent);
```

4. OPPO机型
```
ComponentName cn = new ComponentName("com.coloros.safecenter", "com.coloros.safecenter.startupapp.StartupAppListActivity");
intent.setComponent(cn);
startActivity(intent);
```

5. 魅族机型
魅族同华为一样需要跳转到手机管家页面
```
Intent intent = getPackageManager().getLaunchIntentForPackage("com.meizu.safe");
startActivity(intent);
```


---
title: android的启动
date: 2020-08-05 14:29:44
categories: android
tags:
     - android
description: 讲述了apk从打包到安装再到运行和删除的过程。
---

## android系统的启动过程

## 打包
[参考](https://blog.csdn.net/mysimplelove/article/details/93516904)

## apk的安装
[参考](https://blog.csdn.net/mysimplelove/article/details/93619361)

## apk的运行
1. Launcher activity响应快捷图标点击事件onClick调用 Instrumentation.execStartActivity。（Instrumentation用于监控应用程序和系统的交互）
```
public void onClick(View v) {
    Object tag = v.getTag();
    // 桌面快捷信息
    if (tag instanceof ShortcutInfo) {
        // Open shortcut
        final Intent intent = ((ShortcutInfo) tag).intent;
        int[] pos = new int[2];
        v.getLocationOnScreen(pos);
        intent.setSourceBounds(new Rect(pos[0], pos[1],pos[0] + v.getWidth(), pos[1] + v.getHeight()));
        boolean success = startActivitySafely(v, intent, tag);// 最终执行startActivityForResult
        ...
    }
}
// 这个函数会进行多用户权限判断
public void startActivityForResult(Intent intent, int requestCode, @Nullable Bundle options) {
    if (mParent == null) {
        Instrumentation.ActivityResult ar = mInstrumentation.execStartActivity(this, mMainThread.getApplicationThread(), mToken, this,intent, requestCode, options);
        if (ar != null) {
            mMainThread.sendActivityResult(mToken, mEmbeddedID, requestCode, ar.getResultCode(),ar.getResultData());
        }
        ...
    }
}
```

2. Instrumentation通过ActivityManagerProxy调用到ActivityManagerService.startActivity。
```
public ActivityResult execStartActivity(Context who, IBinder contextThread, IBinder token, Activity target,
    Intent intent, int requestCode, Bundle options) {

    IApplicationThread whoThread = (IApplicationThread) contextThread;
    try {
        intent.migrateExtraStreamToClipData();
        intent.prepareToLeaveProcess();
        // 这里最终调用到ActivityManagerProxy.startActivity
        int result = ActivityManagerNative.getDefault().startActivity(whoThread, who.getBasePackageName(), 
            intent,intent.resolveTypeIfNeeded(who.getContentResolver()),token, target != null ? target.mEmbeddedID : null,requestCode, 0, null, options);
        checkStartActivityResult(result, intent);
    } catch (RemoteException e) {
        throw new RuntimeException("Failure from system", e);
    }
    return null;
}
```

3. ActivityManagerProxy通过Binder调用ActivityManagerService的START_ACTIVITY_TRANSACTION。（ActivityManagerProxy是ActivityManagerService的代理类，它通过Binder将Launcher进程和ActivityManagerService所在的system_server进程通讯）
```
public int startActivity(IApplicationThread caller, String callingPackage, Intent intent,String resolvedType, 
    IBinder resultTo, String resultWho, int requestCode,int startFlags, ProfilerInfo profilerInfo, Bundle options) throws RemoteException {
        ...
        data.writeStrongBinder(resultTo);
        data.writeString(resultWho);
        data.writeInt(requestCode);
        data.writeInt(startFlags);

        if (options != null) {
            data.writeInt(1);
            options.writeToParcel(data, 0);
        }
        ...
        mRemote.transact(START_ACTIVITY_TRANSACTION, data, reply, 0);
        return result;
    }
}
```

4. ActivityManagerService得到我们用Parcelable封装的data后就通过mStackSupervisor（activity栈管理器）会调用startActivityMayWait方法为Launcher启动Activity做准备
```
@Override
public final int startActivityAsUser(IApplicationThread caller, String callingPackage, Intent intent, String resolvedType, 
    IBinder resultTo, String resultWho, int requestCode, int startFlags, ProfilerInfo profilerInfo, Bundle options, int userId) {
    
    enforceNotIsolatedCaller("startActivity");
    userId = handleIncomingUser(Binder.getCallingPid(), Binder.getCallingUid(), userId, false, ALLOW_FULL_ONLY, "startActivity", null);
    return mStackSupervisor.startActivityMayWait(caller, -1, callingPackage, intent, resolvedType, null, null, resultTo, resultWho, 
        requestCode, startFlags,profilerInfo, null, null, options, false, userId, null, null);
}
```

5. ActivityStackSupervisor 随后会调用resumeTopActivityInnerLocked，将Launcher挂起进入pause状态，调用ActivityManagerService创建目标进程。
```
private boolean resumeTopActivityInnerLocked(ActivityRecord prev, Bundle options) {
    // 步入setLaunchSource方法后可以知道，该方法实际是通过PowerManager的setWorkSource方法设置WakeLock，使得在执行后面的工作时系统不会进入休眠状态
    mStackSupervisor.setLaunchSource(next.info.applicationInfo.uid);
    // 现在开始将当前Activity切换到pause状态，使得栈顶Activity可以切换到resume状态
    boolean dontWaitForPause = (next.info.flags&ActivityInfo.FLAG_RESUME_WHILE_PAUSING) != 0;
    // 将后台ActivityStack的Activity切换到pause状态
    boolean pausing = mStackSupervisor.pauseBackStacks(userLeaving, true, dontWaitForPause);
    ...
    ActivityStack lastStack = mStackSupervisor.getLastStack();
    if (next.app != null && next.app.thread != null) {
        // 目标Activity已经可见
        mWindowManager.setAppVisibility(next.appToken, true);
        // 开始创建目标进程
        mStackSupervisor.startSpecificActivityLocked(next, true, true);
        ...
    }
}
//ActivityManagerService 创建目标进程
void startSpecificActivityLocked(ActivityRecord r,boolean andResume, boolean checkConfig) {
    ProcessRecord app = mService.getProcessRecordLocked(r.processName,r.info.applicationInfo.uid, true);
    r.task.stack.setLaunchTime(r);
    ...
    mService.startProcessLocked(r.processName, r.info.applicationInfo, true, 0,"activity", r.intent.getComponent(), false, false, true);
}
```

6. ActivityManagerService 创建进程，并生成ActivityThread。
```
final ProcessRecord startProcessLocked(String processName, ApplicationInfo info, boolean knownToBeDead, 
    int intentFlags, String hostingType, ComponentName hostingName,boolean allowWhileBooting, boolean isolated,
    int isolatedUid, boolean keepIfLarge,String abiOverride, String entryPoint, String[] entryPointArgs, Runnable crashHandler) {
    ProcessRecord app;
    if (!isolated) {
        app = getProcessRecordLocked(processName, info.uid, keepIfLarge);
        ...
    } else {
        // If this is an isolated process, it can't re-use an existing process.
        app = null;
    }
    if (app == null) {
        checkTime(startTime, "startProcess: creating new process record");
        // 创建进程
        app = newProcessRecordLocked(info, processName, isolated, isolatedUid);
        if (app == null) {
            return null;
        }
        app.crashHandler = crashHandler;
        checkTime(startTime, "startProcess: done creating new process record");
    } else {
        // If this is a new package in the process, add the package to the list
        app.addPackage(info.packageName, info.versionCode, mProcessStats);
        checkTime(startTime, "startProcess: added package to existing proc");
    }
    // 开启进程
    startProcessLocked(app, hostingType, hostingNameStr, abiOverride, entryPoint, entryPointArgs);
    return (app.pid != 0) ? app : null;
}
// 开启进程，并创建ActivityThread
private final void startProcessLocked(ProcessRecord app, String hostingType,String hostingNameStr,
     String abiOverride, String entryPoint, String[] entryPointArgs) {
    long startTime = SystemClock.elapsedRealtime();
    if (app.pid > 0 && app.pid != MY_PID) {
        checkTime(startTime, "startProcess: removing from pids map");
        synchronized (mPidsSelfLocked) {
            mPidsSelfLocked.remove(app.pid);
            mHandler.removeMessages(PROC_START_TIMEOUT_MSG, app);
        }
        checkTime(startTime, "startProcess: done removing from pids map");
        app.setPid(0);
    }

    if (DEBUG_PROCESSES && mProcessesOnHold.contains(app)) Slog.v(TAG_PROCESSES,"startProcessLocked removing on hold: " + app);
    mProcessesOnHold.remove(app);
    // Start the process.  It will either succeed and return a result containing the PID of the new process, or else throw a RuntimeException.
    boolean isActivityProcess = (entryPoint == null);
    // 设置了进程创建完成后的入口点（Process.start的参数注释），因此Zygote进程完成了进程创建的操作后就会执行ActivityThread的main()方法
    if (entryPoint == null) entryPoint = "android.app.ActivityThread";
    checkTime(startTime, "startProcess: asking zygote to start proc");
    // Process.start方法创建应用进程是通过 Zygote 进程完成的，设置好参数和创建选项后通过zygoteState.writer将数据交给Zygote进程，它会调用fork()创建进程。
    Process.ProcessStartResult startResult = Process.start(entryPoint,app.processName, uid, uid, gids, 
        debugFlags, mountExternal,app.info.targetSdkVersion, app.info.seinfo, requiredAbi, instructionSet,app.info.dataDir, entryPointArgs);
}
```

7. ActivityThread执行main方法生成handler、looper，开始创建application和activity
```
public static void main(String[] args) {
    Process.setArgV0("<pre-initialized>");
    Looper.prepareMainLooper();
    ActivityThread thread = new ActivityThread();
    // 通知 ActivityManagerService 我们已经创建好了进程和线程，接下来把MainActivity状态切换到resume中，可以打开应用了。
    // 调用ActivityManagerService的attachApplication，由此方法调用StackSupervisor.attachApplicationLocked分别用来创建application和activity
    // 第二步会最终会调用ActivityThread.bindApplication发送Handler消息，在handleMessage方法种调用handleBindApplication进入Application的创建阶段。
    thread.attach(false);

    if (sMainThreadHandler == null) {
        sMainThreadHandler = thread.getHandler();
    }
    if (false) {
        Looper.myLooper().setMessageLogging(newLogPrinter(Log.DEBUG, "ActivityThread"));
    }
    // End of event ActivityThreadMain.
    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
    Looper.loop();
    throw new RuntimeException("Main thread loop unexpectedly exited");
}
// StackSupervisor.attachApplicationLocked分别用来创建application和activity
private final boolean attachApplicationLocked(IApplicationThread thread, int pid) {
    ProcessRecord app;
    if (pid != MY_PID && pid >= 0) {
        synchronized (mPidsSelfLocked) {
            app = mPidsSelfLocked.get(pid);
        }
    }
    app.makeActive(thread, mProcessStats);
    app.curAdj = app.setAdj = -100;
    app.curSchedGroup = app.setSchedGroup = Process.THREAD_GROUP_DEFAULT;
    app.forcingToForeground = null;
    updateProcessForegroundLocked(app, false, false);
    app.hasShownUi = false;
    app.debugging = false;
    app.cached = false;
    app.killedByAm = false;

    mHandler.removeMessages(PROC_START_TIMEOUT_MSG, app);

    // 调用ActivityThread的bindApplication方法
    try {
        thread.bindApplication(processName, appInfo, providers, app.instrumentationClass,profilerInfo, 
            app.instrumentationArguments, app.instrumentationWatcher,app.instrumentationUiAutomationConnection, 
            testMode, enableOpenGlTrace,isRestrictedBackupMode || !normalMode, app.persistent,new Configuration(mConfiguration),
            app.compat,getCommonServicesLocked(app.isolated),mCoreSettingsObserver.getCoreSettingsLocked());
        updateLruProcessLocked(app, false, null);
        app.lastRequestedGc = app.lastLowMemory = SystemClock.uptimeMillis();
    }

    // See if the top visible activity is waiting to run in this process...
    if (normalMode) {
        try {
            if (mStackSupervisor.attachApplicationLocked(app)) {
                didSomething = true;
            }
        } catch (Exception e) {
            Slog.wtf(TAG, "Exception thrown launching activities in " + app, e);
            badApp = true;
        }
    }

    return true;
}
```

8. ActivityThread.handleBindApplication创建Instrumentation、application，最终调用mInstrumentation.callApplicationOnCreate(app)方法出发Application.onCreate()。
```
private void handleBindApplication(AppBindData data) {
    Process.setArgV0(data.processName);
    android.ddm.DdmHandleAppName.setAppName(data.processName, UserHandle.myUserId());
    final ContextImpl appContext = ContextImpl.createAppContext(this, data.info);
    if (data.instrumentationName != null) {
        ...
    } else {
        mInstrumentation = new Instrumentation();
    }
    ...
    try {
        // If the app is being launched for full backup or restore, bring it up in
        // a restricted environment with the base application class.
        Application app = data.info.makeApplication(data.restrictedBackupMode, null);
        mInitialApplication = app;
        ...
        try {
            mInstrumentation.callApplicationOnCreate(app);
        } catch (Exception e) {
            if (!mInstrumentation.onException(app, e)) {
                throw new RuntimeException("Unable to create application " + app.getClass().getName()+ ": " + e.toString(), e);
            }
        }
    } finally {
        StrictMode.setThreadPolicy(savedPolicy);
    }
}
```

9. ActivityThread调用performLaunchActivity出发目标Activity的onCreate、onStart，调用handleResumeActivity使目标Activity进入resume状态。
```
// ActivityStackSupervisor的attachApplicationLocked该方法遍历mActivityDisplays列表得到当前所有ActivityStack，
// 然后取得前台ActivityStack栈顶的ActivityRecord，不为空则启动该对该ActivityRecord调用realStartActivityLocked方法。
// 进入到realStartActivityLocked方法，进行一些前期处理后调用ActivityThread的scheduleLaunchActivity方法，
// 将创建ActivityClientRecord存储我们传入的各种应用相关的数据，通过Handler机制发送。当Handler接收到LAUNCH_ACTIVITY类型的消息时，执行handleLaunchActivity方法。
boolean attachApplicationLocked(ProcessRecord app) throws RemoteException {
    final String processName = app.processName;
    boolean didSomething = false;
    for (int displayNdx = mActivityDisplays.size() - 1; displayNdx >= 0; --displayNdx) {
        ArrayList<ActivityStack> stacks = mActivityDisplays.valueAt(displayNdx).mStacks;
        for (int stackNdx = stacks.size() - 1; stackNdx >= 0; --stackNdx) {
            final ActivityStack stack = stacks.get(stackNdx);
            if (!isFrontStack(stack)) {
                continue;
            }
            ActivityRecord hr = stack.topRunningActivityLocked(null);
            if (hr != null) {
                if (hr.app == null && app.uid == hr.info.applicationInfo.uid
                        && processName.equals(hr.processName)) {
                    try {
                        if (realStartActivityLocked(hr, app, true, true)) {
                            didSomething = true;
                        }
                    } catch (RemoteException e) {
                        Slog.w(TAG, "Exception in new application when starting activity "
                              + hr.intent.getComponent().flattenToShortString(), e);
                        throw e;
                    }
                }
            }
        }
    }
    if (!didSomething) {
        ensureActivitiesVisibleLocked(null, 0);
    }
    return didSomething;
}
```

参考:
[apk的启动](https://blog.csdn.net/mysimplelove/article/details/93722772)
[android线程管理](https://blog.csdn.net/lu1024188315/article/details/75722420)
[Zygote进程的启动](https://www.jianshu.com/p/ab9b83a77af6)



#运用Xposed框架进行测试
-
###Xposed简介
![](/Users/levi/Desktop/xpsed_singal.png)

Xposed框架是一款可以在不修改APK的情况下影响程序运行（修改系统）的框架服务，通过替换/system/bin/app_process程序控制zygote进程，使得app_process在启动过程中会加载XposedBridge.jar这个jar包，从而完成对Zygote进程及其创建的Dalvik虚拟机的劫持。
>zygote是android 系统最最初运行的程序，之后的进程都是通过它fork（你把它理解为复制吧）出来的。 于是zygote中加载的代码，在所有fork出来的子进程都含有（app进程也是fork出来的）。 所以xposed是一个可以hook android系统中任意一个java方法的 hook框架。

-
###Xposed应用
1. 修改替换资源
2. 函数注入

-
###用Xposed开发插件
1. 需要ROOT权限。
2. 安装XposedInstall.apk本地服务应用
3. 下载API库jar包或者在gradle中添加依赖库。![](/Users/levi/Desktop/lib.png)
4. 在Mainfest文件中配置模块相关信息![](/Users/levi/Desktop/setting.png)
5. 新建一个入口类并继承并实现IXposedHookLoadPackage接口
6. 在assets/xposed_init文件下声明主入口路径
7. 编译成功后在xposedinstaller模块管理部分启用![](/Users/levi/Desktop/a.png)
8. 重启验证

-
###常用函数
#####1.findAndHookMethod
```java
findAndHookMethod("java.util.Calendar", lpparam.classLoader,"get",int.class,new XC_MethodHook() {
        @Override
        protected void beforeHookedMethod(MethodHookParam param) throws Throwable {
            // this will be called before the clock was updated by the original method
            XposedBridge.log("Enter->beforeHookedMethod:Calendar.get");
        }
        @Override
        protected void afterHookedMethod(MethodHookParam param) throws Throwable {
            // this will be called after the clock was updated by the original method
            XposedBridge.log("Enter->afterHookedMethod:Calendar.get");
            param.setResult((int)11);
        }
    });
```
#####2.findAndHookConstructor
#####3.initZygote
可选择继承的IXposedHookLoadPackage接口，函数在手机开机时进行hook

-
###著名Xposed模块
#####主题引擎
- ThemeEngine
- HKThemeManager
- CustomText

#####系统美化
- Flat Style Module
- XuiMod
- xSuite
- Holo Themer
- Launcher
- xposed GEL设置

#####SystemUI相关
- All Notifications Expanded
- 重力工具箱:GravityBox : open source
- Tinted Translucent Statusbar: open sourced
- XBatteries : 自定义电池图标
- Tinted Translucent Statusbar : open sourced

#####多窗口
- XMulitWindow
- XHaloFloatingWindow

#####手势控制
- SwipeBack
- 手势导航:Xposed Gesture Navigation
- xposed-DoubleTapToSleep
- Wake Gestures open source!!!

#####锁屏
- maxime widgets
- XLockY

#####系统对话框
- complete action plus

#####其他
- X隐私:XPrivacy: 权限管理:
- Cool tools : 检测系统运行情况，电池等多种功能
- WSL TOOS : 小米的xposed框架
- MinMinLock : 应用加锁


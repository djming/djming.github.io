``` shell
	adb shell dumpsys package {packagename}	//列出对应包名apk的数据
	adb shell am broadcast -a {action}	//发送广播

	adb root && adb remount		//挂载，获取root权限

	adb shell pm list packages	//列出已安装的apk
```

由于 SDK 行为变化和新的 Google Play 政策，Termux 在Play 商店停止更新。 
从 F-Droid 安装应用程序和附加组件。

# 关于Android 10问题的简要说明

Google 要求目标 SDK 等级至少设置为 29，对应
到 Android OS 版本 10。但是由于新的操作系统行为变化，我们
不能这样做，必须使用 SDK 28。

https://developer.android.com/about/versions/10/behavior-changes-10#execute-permission
> 针对 Android 10 的不受信任的应用程序无法在其中的文件上调用 exec()
应用程序的主目录，从可写应用程序目录执行文件
目录是 W^X 违规。 应用程序应该只加载二进制代码嵌入在应用程序的 APK 文件中。

相关问题 : https://github.com/termux/termux-app/issues/1072

*我们不想引入破坏性更改以符合 Google 的
要求。 但结果是 Termux 正式不支持 Android 10。*

请注意，Android 10 用户可能会面临以下限制：
* 在某些设备上，Termux 不会启动 shell。 解决方法是设置
   SELinux 到许可模式 - 只能在有根设备上使用。

* 对 `/proc/net` 的访问受到限制。 结果`netstat`和其他
   使用此界面数据的实用程序不再起作用。
   在根设备上以超级用户身份运行这些实用程序以获取它们
   再次工作。

使用 Android 11 的用户可能会遇到更多问题，例如一些
实用程序正在被文件描述符清理程序终止。
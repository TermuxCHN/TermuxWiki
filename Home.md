# Termux 开发者维基

有关使用 Termux 构建环境、移植包和APT 存储库。

用户维基：https://wiki.termux.com/wiki/Main_Page。

## Git存储库

属于 Termux 的所有源代码都在以下可用的存储库之中找到。
组织：[Github](https://github.com): https://github.com/termux。

维护Termux项目的镜像 [Gitlab](https://gitlab.com/termux-mirror)。

*自 2020 年 1 月 1 日起停止支持 Android 操作系统版本 5.x - 6.x！*

### 应用程序和插件

所有 apk 都通过[Google Play](https://play.google.com/store/apps/developer?id=Fredrik+Fornwall), [F-Droid](https://search.f-droid.org/?q=Termux)和[Kali Nethunter Store](https://store.nethunter.com/en/packages/#q=Termux)。

F-Droid 和 Kali Nethunter Store 上的构建是独立完成的，并使用不同的证书签名。
因此，不能混合从不同来源获得的应用程序和插件。

* [termux-app](https://github.com/termux/termux-app): Termux源代码
* [termux-api](https://github.com/termux/termux-api): Termux:API 插件源代码
* [termux-boot](https://github.com/termux/termux-boot): Termux:Boot 插件源代码
* [termux-failsafe](https://github.com/termux/termux-failsafe): Termux 故障安全模式源代码
* [termux-float](https://github.com/termux/termux-float): Termux:Float 插件源代码
* [termux-styling](https://github.com/termux/termux-styling): Termux:Styling 插件源代码
* [termux-tasker](https://github.com/termux/termux-tasker): Termux:Tasker 插件源代码
* [termux-x11](https://github.com/termux/termux-x11): Termux:X11 插件源代码，不维护

### 包

构建脚本和补丁在多个存储库之间拆分，取决于它们的功能。

* [termux-packages](https://github.com/termux/termux-packages): 主存储库。
* [termux-root-packages](https://github.com/termux/termux-root-packages): 适合root设备的存储库。
* [x11-packages](https://github.com/termux/x11-packages): 图形化(x11)存储库

### 特殊包的存储库

我们维护一些用于特殊目的的项目。

Packages:
* [TermuxAm](https://github.com/termux/TermuxAm): Android 操作系统的活动管理器。
* [command-not-found](https://github.com/termux/command-not-found): Termux的command not found。
* [libandroid-shmem](https://github.com/termux/libandroid-shmem): System V 通过 Android 的 ashmem 实现共享内存。
* [libandroid-support](https://github.com/termux/libandroid-support): 仿生 libc 中缺少的功能的实现。
* [play-audio](https://github.com/termux/play-audio): 通过 OpenSLES 播放音频文件的实用程序。
* [proot](https://github.com/termux/proot): 模拟root环境，从[proot](https://proot-me.github.io/) fork。
* [termux-api-package](https://github.com/termux/termux-api-package): Termux:API 的命令行接口。
* [termux-apt-repo](https://github.com/termux/termux-apt-repo): 用于从指定包生成 APT 存储库的脚本。
* [termux-auth](https://github.com/termux/termux-auth): 用于 OpenSSH 和类似软件包的密码验证实现。
* [termux-create-package](https://github.com/termux/termux-create-package): 从一组文件和元数据定义创建适用于Termux的 DEB 包。
* [termux-elf-cleaner](https://github.com/termux/termux-elf-cleaner): 从 ELF 二进制文件中删除未使用的部分。
* [termux-exec](https://github.com/termux/termux-exec): `LD_PRELOAD` 挂钩，用于在执行 `execve()` 系统调用期间修复 `/bin` 和 `/sbin` 路径访问。
* [termux-services](https://github.com/termux/termux-services): 用于在 Termux 中管理服务的脚本。

Other:
* [termux.github.io](https://github.com/termux/termux.github.io): Termux官网
* [termux-packaging](https://github.com/termux/termux-packaging): 用于处理包和生成引导存档。

### 外部资源链接

- [Android changes for NDK developers](https://android.googlesource.com/platform/bionic/+/master/android-changes-for-ndk-developers.md)
- [Linux From Scratch](http://www.linuxfromscratch.org/lfs/view/stable/)
- [Beyond Linux From Scratch](http://www.linuxfromscratch.org/blfs/view/stable/)
- [OpenWrt](https://openwrt.org/) 作为嵌入式 Linx 发行版包含 [patches and build scripts](https://dev.openwrt.org/browser/packages)
- [Kivy recipes](https://github.com/kivy/python-for-android/tree/master/pythonforandroid/recipes) 包含为 Android 构建包的配方。
- [Repology](https://repology.org) 跟踪过时的软件包。

Android OS 通常不提供对系统目录的写入权限，例如根文件系统 ("/")
出于系统文件的安全性和完整性的原因。 这使得难以遵循
[文件系统层次标准](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)， Termux
必须使用自己的。

## 安装root包

所有软件包都必须将其数据安装到此目录中（安装前缀）：
```
/data/data/com.termux/files/usr
```
为了用户数据的安全，不允许在此目录之外创建安装文件的包。

我们经常将此路径称为“$PREFIX”或“$TERMUX_PREFIX”，后者用于打包的上下文中。

#### Termux 文件系统层次表

| 路径                          | 目的             |
|:------------------------------|:-----------------|
|`${TERMUX_PREFIX}/bin`         | shell 使用的可执行文件。 结合 `/bin`, `/sbin`, `/usr/bin`, `/usr/sbin`.|
|`${TERMUX_PREFIX}/etc`         | 配置文件.|
|`${TERMUX_PREFIX}/include`     | C/C++头文件.|
|`${TERMUX_PREFIX}/lib`         | 共享对象（库）、运行时可执行数据或开发相关.|
|`${TERMUX_PREFIX}/libexec`     | 不应由用户直接运行的可执行文件.|
|`${TERMUX_PREFIX}/opt`         | 可选包的安装根目录.|
|`${TERMUX_PREFIX}/share`       | 不可执行的运行时数据和文档.|
|`${TERMUX_PREFIX}/tmp`         | 临时文件。 在每次应用程序重新启动时擦除。 结合 `/tmp` and `/var/tmp`. *用户可以自由修改。*|
|`${TERMUX_PREFIX}/var`         | 可变数据，例如缓存和数据库. *可由用户修改，但要格外小心。*|
|`${TERMUX_PREFIX}/var/run`     | 锁定文件、PID 文件、套接字和其他由守护进程创建的临时文件。 替换 `/run`.|

>重要提示：不要被前缀目录 `.../usr` 所迷惑。 它与真正的`/usr`无关！
您可以在 Linux 发行版中找到的目录。 Termux 从不使用辅助文件系统层次结构
(`/usr`) 用于打包目的。

应修补对 FHS 目录的所有硬编码引用。

## 家目录

Termux 主目录位于包安装前缀之外，位于以下路径：
```
/data/data/com.termux/files/home
```

这是应该存储所有用户数据的地方。 由于通常存储所有应用程序内部数据
在 EXT4 或 F2FS 文件系统上，它支持文件访问模式、可执行权限和特殊文件，如
符号链接。

包不应该将文件安装到主目录。 例外仅适用于 .deb 文件脚本，它们
可用于在 $HOME 中为无法自行完成的软件包准备初始配置。

## 安卓目录

Android OS 提供了许多目录，其中一些是 FHS 兼容的。 所有系统目录都是
只读和软件包永远不应尝试安装或删除其中的某些内容。

| 路径 | 描述                                       |
|------|---------------------------------------------------|
|`/`   | 根文件系统。 通常它是一个 ramdisk，但在现代 Android 操作系统版本上它是一个挂载的系统分区。 可以被 SELinux 限制，不能被 `ls` 。|
|`/bin`| 指向“/system/bin”的符号链接。 不要将此添加到 PATH 以防止 Termux 工具与 Android 提供的工具发生冲突。|
|`/dev`| 带有设备文件的文件系统的标准挂载点。 SELinux 可以限制访问，尽管所有重要的全局可写设备都可以访问。|
|`/etc`| 指向`/system/etc`的符号链接。|
|`/mnt`| 具有应用程序和用户数据的文件系统的原始挂载点。|
|`/proc`| 包含运行时进程和内核信息的标准目录。 通常使用 `hidepid=2` 选项安装以保护隐私。|
|`/proc/net`| 网络接口统计。 出于隐私原因，自 Android 10 起访问受限。|
|`/sbin`| 特殊用途可执行文件（ADB 守护程序、dm-verity 帮助程序、调制解调器 nvram 加载程序等）所在的目录。 访问受 SELinux 和文件模式限制。 不要将此目录添加到 PATH。|
|`/storage`| 挂载用户的存储卷。 与 `/mnt` 类似，但驱动器文件系统由 `sdcardfs` 守护进程提供。|
|`/system`| Android OS 安装根目录.|
|`/system/bin`|一套用于系统用途的基本命令行工具和功能齐全的 ADB shell。 避免将其添加到 PATH。 只有备用可执行路径允许例外，例如 如果未安装软件包。|
|`/system/xbin`| 可选的系统命令行工具集。 ROM 之间的内容可能有所不同。 不要将此添加到 PATH。|
## 一般的

- 大多数程序期望目标是 [FHS](https://uk.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)
  合规。它们有硬编码的路径，例如`/etc`、`/bin`、`/usr/share`、`/tmp`
  它们在标准位置的 Termux 中不可用，而仅在 `$TERMUX_PREFIX` 中可用。

- Android 仿生 libc 没有 iconv 和 gettext/libintl 功能
  内置的。一个`libandroid-support`包包含这些并且可以被所有人使用
  包。

- “错误：z：没有存档符号表（运行ranlib）”通常意味着构建
  使用机器的 libz 而不是用于交叉编译的 libz，因为
  构建器库 -L 路径设置不正确。

- rindex(3) 不存在，但 strrchr(3) 无论如何都是首选。

- &lt;sys/termios.h&gt;不存在，但 &lt;termios.h&gt;是标准
  地点。

- &lt;sys/fcntl.h&gt;不存在，但是 &lt;fcntl.h&gt;是标准
  地点。

- &lt;sys/timeb.h&gt;不存在（在 POSIX 2008 中删除），但 ftime(3) 可以
  替换为 gettimeofday(2)。

- &lt;glob.h&gt;不存在，但可以通过 `libandroid-glob` 获得
  包裹。

- 内核不支持 SYSV 共享内存。一个`libandroid-shmem`
  包，它在 [ashmem](http://elinux.org/Android_Kernel_Features#ashmem) 之上模拟 SYSV 共享内存
  共享内存系统，可用。与 `LDFLAGS+=" -landroid-shmem` 一起使用。

- 内核不支持 SYSV 信号量。 `libandroid-posix-semaphore` 包支持命名信号量和函数，例如 `sem_open`、`sem_close` 和 `sem_unlink`。

- 从 Android 8 开始，一个 [Seccomp](https://android-developers.googleblog.com/2017/07/seccomp-filter-in-android-o.html)
  已为应用程序启用。 Seccomp 禁止使用某些系统调用
  这会导致崩溃并出现“错误的系统调用”错误。

- 从 Android 8 开始，程序不能使用 `tcsetattr()` 和 `TCSAFLUSH`
  由于 SELinux 的参数。请改用“TCSANOW”。

- 从 Android 9 开始，[Seccomp](https://android-developers.googleblog.com/2017/07/seccomp-filter-in-android-o.html)
  开始阻止与`setuid()`相关的系统调用。由于 Termux 主要用于
  单用户非 root 用户，无论如何不鼓励使用 setuid/setgid 功能。

## dlopen() and RTLD&#95;&#42; flags

&lt;dlfcn.h&gt; declares
```C
RTLD_NOW=0; RTLD_LAZY=1; RTLD_LOCAL=0; RTLD_GLOBAL=2;       RTLD_NOLOAD=4; // 32-bit
RTLD_NOW=2; RTLD_LAZY=1; RTLD_LOCAL=0; RTLD_GLOBAL=0x00100; RTLD_NOLOAD=4; // 64-bit
```
这些与 glibc 的不同之处在于

1.它们与glibc的值不同，因此不能在文件中硬编码
    （python 中的 DLFCN.py 就是这样做的）

2. 他们缺少一些值（`RTLD_BINDING_MASK`, ...）

## Android 动态链接器

Android 动态链接器位于 `/system/bin/linker`（32 位）或
`/system/bin/linker64`（64 位）。 以下是不同版本的源链接
链接器：

- [Android 5.0 linker](https://android.googlesource.com/platform/bionic/+/lollipop-mr1-release/linker/linker.cpp)

- [Android 6.0 linker](https://android.googlesource.com/platform/bionic/+/marshmallow-mr1-release/linker/linker.cpp)

- [Android 7.0 linker](https://android.googlesource.com/platform/bionic/+/nougat-mr1-release/linker/linker.cpp)

关于链接器的一些注意事项：

- 链接器警告未使用的 [动态部分条目](https://docs.oracle.com/cd/E23824_01/html/819-0690/chapter6-42444.html)
  带有“警告：链接器：$BINARY：未使用的 DT 条目：类型 ${VALUE_OF_d_tag}”
  信息。

- 支持的动态部分条目类型随着时间的推移而增加。

- Termux 构建系统使用 [termux-elf-cleaner](https://github.com/termux/termux-elf-cleaner)
  剥离导致上述链接器警告的未使用的 ELF 条目。

- 符号版本控制仅从 Android 6.0 开始受支持，因此被剥离。

- `DT_RPATH`，链接器应在其中查找共享的目录列表
  不支持库，因此被剥离。

- `DT_RUNPATH`，与上面相同，但在`LD_LIBRARY_PATH`之后查看，是
  仅从 Android 7.0 开始支持，因此被剥离。

- 使用 `dlopen()` 打开共享库时的符号可见性有效
  不同。在普通链接器上，当可执行文件链接到共享
  library libA dlopen():s 另一个共享库 libB，libA 的符号为
  暴露给 libB 而不需要 libB 显式链接 libA。这
  不适用于 Android 链接器，这可能会破坏插件系统
  主要可执行文件 dlopen():s 一个插件，它没有显式链接到
  一些已由可执行文件链接到的共享库。
  请参阅[相关的 NDK 问题](https://github.com/android-ndk/ndk/issues/201)
  了解更多信息。
## 一般要求

- 没有需要 root 权限的操作。

- 没有修改构建目录或 `$TERMUX_PREFIX` 之外的文件的操作。

- 所有脚本必须按照 [编码指南](./Coding-guideline) 进行格式化。

## 写一个软件包包脚本

软件包包脚本 (`build.sh`) 是一个 Bash 脚本，其中包含定义
元数据和（如果需要）Termux 构建系统的说明如何构建
包裹。 此类脚本在内部 [使用](./Building-packages#understanding-build-process)
通过`./build-package.sh` 并且不是独立的。

构建软件包包脚本的实例:
```
# 软件包主页
TERMUX_PKG_HOMEPAGE=https://www.gnu.org/software/ed/

# 一行且简短的软件包描述
TERMUX_PKG_DESCRIPTION="Classic UNIX line editor"

# 许可证
TERMUX_PKG_LICENSE="GPL-2.0"

# 谁维护这个包
# 如果您希望维护(请指定你的GitHub昵称)
# 包，修复它的错误等。否则指定“@termux”。
# 请注意，非官方存储库不允许引用@termux
# 作为他们的维护者。
TERMUX_PKG_MAINTAINER="@termux"

# 软件包版本
TERMUX_PKG_VERSION=1.15

# 源代码URL
TERMUX_PKG_SRCURL=https://mirrors.kernel.org/gnu/ed/ed-${TERMUX_PKG_VERSION}.tar.lz

# 源代码URL的sha256
# Windows：certutil -hashfile <文件名> SHA256
# Linux： sha256sum <文件名>
TERMUX_PKG_SHA256=ad4489c0ad7a108c514262da28e6c2a426946fb408a3977ef1ed34308bdfd174
```
请注意，上面显示的字段顺序是首选。 在上面的例子中也
没有预期的构建步骤覆盖内部步骤足以成功
构建包。

`./build-package.sh` 可以自动检测以下构建系统：

- Autotools

- CMake

- Meson

如果您需要传递一些额外的参数，请使用该字段：
`TERMUX_PKG_EXTRA_CONFIGURE_ARGS`.

### 可用包控制字段表

| 命令 | 变量 | 必需的 | 描述 |
| -----:|:-------- |:--------:|:----------- |
| 1     | `TERMUX_PKG_HOMEPAGE` | yes | 主页URL. |
| 2     | `TERMUX_PKG_DESCRIPTION` | yes | 软件包描述 |
| 3     | `TERMUX_PKG_LICENSE` | yes | 软件包许可证. |
| 4     | `TERMUX_PKG_LICENSE_FILE` | no | 许可证文件的名称，如果没有自动找到. |
| 5     | `TERMUX_PKG_MAINTAINER` | no | 软件包维护者. |
| 6     | `TERMUX_PKG_API_LEVEL` | no | 应为其编译包的 Android API 级别. |
| 7     | `TERMUX_PKG_VERSION` | yes | 原始软件包版本. |
| 8     | `TERMUX_PKG_REVISION` | no | 包修订。 每次包重建时都会遇到问题. |
| 9     | `TERMUX_PKG_SKIP_SRC_EXTRACT` | no | 是否省略源代码下载和提取。 默认为**false**. |
| 10    | `TERMUX_PKG_SRCURL` | not, 如果跳过源提取 | 应该从中下载源存档的 URL，存档或以 .git 结尾的 git url |
| 11    | `TERMUX_PKG_SHA256` | not, 如果未设置源 URL | 源存档的 SHA-256 校验和。 |
| 12    | `TERMUX_PKG_GIT_BRANCH` | no | 分支到 termux_step_git_clone_src 中的checkout。 默认为`v$TERMUX_PKG_VERSION`。 |
| 13    | `TERMUX_PKG_METAPACKAGE` | no | 是否将包视为元包。 默认为**false**。 |
| 14    | `TERMUX_PKG_DEPENDS` | no | 软件包依赖 |
| 15    | `TERMUX_PKG_BUILD_DEPENDS` | no | 构建需要的软件包依赖. |
| 16    | `TERMUX_PKG_BREAKS` | no | 与当前不兼容的包的逗号分隔列表. |
| 17    | `TERMUX_PKG_CONFLICTS` | no | 与当前文件名冲突的包的逗号分隔列表. |
| 18    | `TERMUX_PKG_REPLACES` | no | 被当前替换的包的逗号分隔列表. |
| 19    | `TERMUX_PKG_PROVIDES` | no | 当前提供的虚拟包的逗号分隔列表. |
| 20    | `TERMUX_PKG_RECOMMENDS` | no | 非绝对依赖项的逗号分隔列表 - 通常与当前依赖项一起使用的包. |
| 21    | `TERMUX_PKG_SUGGESTS` | no | 与当前软件包相关或增强当前软件包的逗号分隔列表. |
| 22    | `TERMUX_PKG_ESSENTIAL` | no | 是否将无法以通常方式卸载的软件包视为必不可少的。 默认为**false**. |
| 23    | `TERMUX_PKG_NO_STATICSPLIT` | no | 是否将静态库拆分为子包。 默认为**false**. |
| 24    | `TERMUX_PKG_BUILD_IN_SRC` | no | 是否在源代码目录中执行构建。 默认为**false**. |
| 25    | `TERMUX_PKG_HAS_DEBUG` | no | 是否可以为包进行调试构建。 默认为**true**. |
| 26    | `TERMUX_PKG_PLATFORM_INDEPENDENT` | no | 是否将包视为平台无关。 默认为**false**. |
| 27    | `TERMUX_PKG_BLACKLISTED_ARCHES` | no | 无法为其编译包的 CPU 架构的逗号分隔列表. |
| 28    | `TERMUX_PKG_HOSTBUILD` | no | 包是否需要为主机构建。 默认为**false**. |
| 29    | `TERMUX_PKG_FORCE_CMAKE` | no | 是否更适合 CMake 而不是 Autotools 配置脚本。 默认为**false**. |
| 30    | `TERMUX_PKG_EXTRA_CONFIGURE_ARGS` | no | 传递给构建系统配置实用程序的额外参数. |
| 31    | `TERMUX_PKG_EXTRA_HOSTBUILD_CONFIGURE_ARGS` | no | 执行主机构建时传递给构建系统配置实用程序的额外参数. |
| 32    | `TERMUX_PKG_EXTRA_MAKE_ARGS` | no | 传递给实用程序“make”的额外参数. |
| 33    | `TERMUX_PKG_MAKE_INSTALL_TARGET` | no | 等效于在安装过程中传递给实用程序 `make` 的 `install` 参数. |
| 34    | `TERMUX_PKG_RM_AFTER_INSTALL` | no | 安装过程后应删除的文件列表. |
| 35    | `TERMUX_PKG_CONFFILES` | no | 更新时不应覆盖的包配置文件的空格或换行符列表. |
| 36    | `TERMUX_PKG_SERVICE_SCRIPT` | no | 包含守护进程名称和脚本的偶数长度数组，用于 [termux-services/runit](https://wiki.termux.com/wiki/Termux-services). |
| 37    | `TERMUX_PKG_AUTO_UPDATE` | no | 是否开启此包的自动更新（目前仅针对在 GitHub 上开发的包） |
| 38    | `TERMUX_PKG_AUTO_UPDATE_TAG_REGEXP` | no | 用于验证新版本的正则表达式。 当上游维护多个主要版本时很有用 |

### 可用构建步骤覆盖表

所有构建步骤的完整参考可以在
[Building packages](./Building-packages#build-steps-reference)找到.

| 执行顺序 | 函数名 | 描述 |
| ---------------:|:-------------:|:----------- |
| 1               | `termux_step_get_source` | 获取包源代码并放入`$TERMUX_PKG_SRCDIR`. |
| 2               | `termux_step_post_get_source` | 获取源代码后立即运行命令的钩子. |
| 3               | `termux_step_handle_host_build` | 确定是否需要主机构建. |
| 4               | `termux_step_host_build` | 执行主机构建. |
| 5               | `termux_step_pre_configure` | 在源配置之前挂钩运行命令. |
| 6               | `termux_step_configure` | 配置来源。 默认情况下，它会自动确定构建系统. |
| 7               | `termux_step_post_configure` | 配置后立即运行命令的挂钩. |
| 8               | `termux_step_make` | 编译源代码. |
| 9               | `termux_step_make_install` | 安装编译的软件. |
| 10              | `termux_step_post_make_install` | 安装后立即运行命令的挂钩. |
| 11              | `termux_step_install_license` | 链接或复制特定包的许可证到`./share/doc/$TERMUX_PKG_NAME`. |
| 12              | `termux_step_post_massage` | 创建 `*.deb` 文件之前的最后一个钩子. |
| 13              | `termux_step_create_debscripts` | 在此步骤中，您可以创建 ./preinst、./postinst、./prerm 或 ./postrm 脚本，这些脚本将在软件包安装或删除期间执行. |

## 编写分包脚本

子包定义通常用于将安装的可选部分移动到
一个单独的包。 例如，一些库带有实用程序，这些实用程序可能
不被最终用户使用。 因此，我们可以将这些实用程序移动到单独的包中
并在库包安装为的情况下减小安装大小
依赖。

最小分包脚本包含以下字段：
```
TERMUX_SUBPKG_DESCRIPTION= # 子包描述
TERMUX_SUBPKG_INCLUDE="" # 包含在子包中的文件列表（空格或换行符分隔）
```
上面的顺序是首选，因为包含列表可能很长。

子包脚本必须位于与`build.sh`相同的目录并且有文件
名称格式如下：
```
{subpackage name}.subpackage.sh
```
请注意，它的名称不能与父包的名称相同。

关于子包的附加说明：

- 子包的版本总是与父包相同。

- 静态库的子包是自动创建的。

### 可用子包控制字段表

| 命令 | 变量 | 依赖 | 描述 |
| -----:|:-------- |:--------:|:----------- |
| 1     | `TERMUX_SUBPKG_DESCRIPTION` | yes | 子包的简短的单行描述。 |
| 2     | `TERMUX_SUBPKG_DEPEND_ON_PARENT` | no | 指定子包应如何依赖于父级的方式。 有关详细信息，请参阅 [子包依赖项](#subpackage-dependencies). |
| 3     | `TERMUX_SUBPKG_DEPENDS` | no | 逗号分隔的子包依赖列表. |
| 4     | `TERMUX_SUBPKG_BREAKS` | no | 与当前不兼容的包的逗号分隔列表. |
| 5     | `TERMUX_SUBPKG_CONFLICTS` | no | 与当前文件名冲突的包的逗号分隔列表. |
| 6     | `TERMUX_SUBPKG_REPLACES` | no | 被当前替换的包的逗号分隔列表. |
| 7     | `TERMUX_SUBPKG_ESSENTIAL` | no | 是否将无法按常规方式卸载的子包视为必不可少的。 默认为**false**. |
| 8     | `TERMUX_SUBPKG_PLATFORM_INDEPENDENT` | no | 是否将子包视为平台无关。 默认为**false**. |
| 9     | `TERMUX_SUBPKG_INCLUDE` | yes | 要包含在子包中的以空格或换行符分隔的文件列表. |
| 10    | `TERMUX_SUBPKG_CONFFILES` | no | 更新时不应覆盖的包配置文件的空格或换行符列表. |

#### 子包依赖

默认情况下，子包仅依赖于当前版本的父包。这
可以通过设置变量 `TERMUX_SUBPKG_DEPEND_ON_PARENT` 来更改行为。

允许的值为：

- `deps` - 子包将依赖于父包的依赖。

- `unversioned` - 子包将依赖父包而没有指定
  版本。

## 包脚本中的保留变量

在上面列出的变量（即控制字段）中，某些变量具有
特殊用途，由`./build-package.sh`内部使用。他们不应该
除非有充分的理由，否则在运行时进行修改。

- `TERMUX_ON_DEVICE_BUILD` - 如果设置，假设在设备上构建。

- `TERMUX_BUILD_IGNORE_LOCK` - 如果设置为 `true`，则忽略构建过程锁定。

- `TERMUX_BUILD_LOCK_FILE` - 构建进程锁定文件的路径。

- `TERMUX_HOST_PLATFORM` - 主机平台定义。通常是`$TERMUX_ARCH-linux-android`。

- `TERMUX_PKG_BUILDDIR` - 当前包的构建目录的路径。

- `TERMUX_PKG_BUILDER_DIR` - `build.sh` 所在目录的路径
  当前包。

- `TERMUX_PKG_BUILDER_SCRIPT` - 当前包的`build.sh`的路径。

- `TERMUX_PKG_CACHEDIR` - 当前包的源缓存目录的路径。

- `TERMUX_PKG_MASSAGEDIR` - 包内容所在目录的路径
  从“$TERMUX_PREFIX”中提取。

- `TERMUX_PKG_PACKAGEDIR` - `*.deb` 存档组件的目录路径
  的当前包将被创建。

- `TERMUX_PKG_SRCDIR` - 当前包的源目录的路径。

- `TERMUX_PKG_TMPDIR` - 特定于当前包的临时目录的路径。

- `TERMUX_COMMON_CACHEDIR` - 构建工具所在的全局缓存目录的路径
  被存储。

- `TERMUX_SCRIPTDIR` - 带有实用程序脚本的目录路径。

- `TERMUX_PKG_NAME` - 当前包的名称。

- `TERMUX_REPO_URL` - APT 存储库 URL 的数组，依赖项将来自这些 URL
  如果 `./build-package.sh` 有选项 `-i` 或 `-I`，则下载。

- `TERMUX_REPO_DISTRIBUTION` - 分布名称数组
  `TERMUX_REPO_URL`。

- `TERMUX_REPO_COMPONENT` - 存储库组件名称数组
  `TERMUX_REPO_URL`。

- `TERMUX_PACKAGE_FORMAT` - 包输出格式。
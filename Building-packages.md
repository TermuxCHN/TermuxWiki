[![asciicast](https://asciinema.org/a/5BsCbNoCzNFEgcH8vh8975MLm.svg)](https://asciinema.org/a/5BsCbNoCzNFEgcH8vh8975MLm?autoplay=1&speed=2.5)

##如何构建包

一旦你正确[配置](https://github.com/termux/termux-packages/wiki/Build-environment)
构建环境，您可以开始构建包。 构建开始于
执行位于 termux-packages 根目录的脚本`build-package.sh`
将包名称作为参数传递的存储库。

为架构 `arm` 构建包 `bash` 的示例：
```
./build-package.sh -a arm bash
```

你需要构建几个包，在命令行上指定它们。
例子：
```
./build-package.sh bash coreutils htop
```

### 构建选项

脚本 `build-package.sh` 接受几个影响构建行为的选项。

- `-a` - 要构建的架构。接受的值为：aarch64、arm、i686、
  x86_64。默认为“aarch64”。对于设备上的构建架构已确定
  自动，此选项不可用。

- `-d` - 使用调试信息和编译器优化进行构建。

- `-D` - 构建禁用包，即位于目录 `./disabled-packages` 中。

- `-f` - 强制构建包，即使它已经构建或安装。

- `-i` - 下载依赖项而不是构建它们。擦除数据
  开始构建之前的`TERMUX_PREFIX`。不适用于设备上的构建。

- `-I` - 下载依赖项而不是构建它们。与上述选项不同，
  这个不会擦除 `$TERMUX_PREFIX` 中的数据并且可用于设备上
  构建。

- `-q` - 将必要的参数传递给 `make` 或类似工具以使构建安静。
  可能不适用于所有软件包。

- `-c` - 继续之前的构建，跳过源代码提取和配置步骤，直接进入 termux_step_make。仅当您第一次运行正常构建并且它失败或者您在 termux_step_make 或以后停止它时才有效。

- `-s` - 跳过依赖检查。

- `-o` - 指定放置构建的 \*.deb 文件的目录。默认为
  `./输出`。

- `--format` - 指定构建包的格式。可能的：`debian`（默认）和`pacman`。

## 了解构建过程

构建在 `./build-package.sh` 脚本执行时开始，并且是
分为以下几个阶段（环境初始化步骤省略）：

1. source `./packages/$TERMUX_PACKAGE_NAME/build.sh` 获取元数据
   例如版本、描述、依赖项、源 URL 和特定于包的
   构建步骤。

2. 如果 `build-package.sh` 有参数 `-i` 或 `-I`，请下载 \*.deb
   从 APT 存储库中提取文件并提取到“$TERMUX_PREFIX”。

   否则从脚本`./scripts/buildorder.py`获取依赖构建顺序
   并为每个依赖包执行带有参数“-s”的“./build-package.sh”。

3. 创建时间戳文件，稍后使用。

4. 从 URL 下载包的源代码并验证其 SHA-256 校验和。
   然后将源代码提取到`$TERMUX_PKG_SRCDIR`。如果这一步省略
   `$TERMUX_PKG_SKIP_SRC_EXTRACT` 已设置。

5. 为主机构建包。此步骤仅在以下情况下执行
   `TERMUX_PKG_HOSTBUILD` 已设置。

6. 设置独立的 Android NDK 工具链并应用 NDK sysroot 补丁。
   此步骤仅执行一次。

7. 在 `./packages/$TERMUX_PKG_NAME/*.patch` 中搜索补丁并应用它们。

8. 为指定的`$TERMUX_ARCH`配置和编译源代码。

9. 将所有内容安装到`$TERMUX_PREFIX`。

10.确定从时间戳文件创建时修改的文件
    （第 3 步）并将它们提取到 `$TERMUX_TOPDIR/$TERMUX_PKG_NAME/massage`。

11. 剥离二进制文件（如果是非调试版本）、gzip 手册页、删除不需要的文件
    并修复权限。如果需要，在子包之间分发文件。

12. 将包数据归档到 \*.deb 文件中，以便分发。


## 构建步骤参考

顺序指定功能顺序。 0 order 指定效用函数。

Suborder 指定由主函数触发的函数。 功能与
不同的子订单不会同时执行。

| 命令 | 函数名称 | 可覆盖 | 描述 |
| -----:|:-------------:| -----------:|:----------- |
| 0.1   | `termux_error_exit` | no | 停止脚本并输出错误. |
| 0.2   | `termux_download` | no | 下载任何文件的实用功能. |
| 0.3   | `termux_setup_golang` | no | 设置 Go 构建环境. |
| 0.4   | `termux_setup_cmake` | no | 设置Cmake构建环境. |
| 0.5   | `termux_setup_ninja` | no | 设置Ninja构建环境. |
| 0.6   | `termux_setup_meson` | no | 设置Meson构建环境. |
| 0.7   | `termux_setup_protobuf` | no | 设置Protobuf编译器. |
| 0.8   | `termux_setup_rust` | no | 设置Cargo构建. |
| 1     | `termux_step_setup_variables` | no | 设置基本变量，如目录位置和标志. |
| 2     | `termux_step_handle_buildarch` | no | 确定要构建的架构. |
| 3     | `termux_step_setup_build_folders` | no | 删除旧的 src 并构建目录（如果存在）. |
| 4     | `termux_step_start_build` | no | 初始化构建环境。 源包的`build.sh`. |
| 5     | `termux_step_get_dependencies` | no | 下载或构建包的指定依赖项. |
| 5.1   | `termux_step_get_repo_files` | no | 提供 `-i` 或 `-I` 选项时获取 APT 包信息. |
| 5.2   | `termux_extract_dep_info` | no | 获取包架构和版本以供下载. |
| 6     | `termux_step_create_timestamp_file` | no | 制作时间戳以确定构建已经安装了哪些文件. |
| 5.3   | `termux_download_deb` | no | 下载依赖 `*.deb` 包进行安装. |
| 6     | `termux_step_get_source` | yes | 获取包源代码并放入`$TERMUX_PKG_SRCDIR`. |
| 7.1   | `termux_git_clone_src` | no | 通过 git clone 获取源码，如果 `$TERMUX_PKG_SRCURL` 以 ".git" 结尾则运行. |
| 7.2   | `termux_download_src_archive` | no | 下载带有包源代码的 zip 或 tar 存档. |
| 7.3   | `termux_unpack_src_archive` | no | 将下载的存档解压缩到`$TERMUX_PKG_SRCDIR`. |
| 8     | `termux_step_post_get_source` | yes | 获取源代码后立即运行命令的钩子. |
| 9     | `termux_step_handle_host_build` | yes | 确定是否需要主机构建. |
| 9.1   | `termux_step_host_build` | yes | 执行主机构建. |
| 10     | `termux_step_setup_toolchain` | no | 设置 NDK 独立工具链. |
| 11    | `termux_step_patch_package` | no | 应用于源代码包目录中的所有 `*.patch` 文件. |
| 12    | `termux_step_replace_guess_scripts` | no | 替换 `config.sub` 和 `config.guess` 脚本. |
| 13    | `termux_step_pre_configure` | yes | 在源配置之前挂钩运行命令. |
| 14    | `termux_step_configure` | yes | 配置来源。 默认情况下，它会自动确定构建系统. |
| 14.1  | `termux_step_configure_autotools` | no | Autotools 构建配置. |
| 14.2  | `termux_step_configure_cmake` | no | CMake构建配置. |
| 14.3  | `termux_step_configure_meson` | no | Meson构建配置. |
| 15    | `termux_step_post_configure` | yes | 配置后立即运行命令的挂钩. |
| 16    | `termux_step_make` | yes | 编译源代码. |
| 17    | `termux_step_make_install` | yes | 安装编译的工件. |
| 18    | `termux_step_post_make_install` | yes | 安装后立即运行命令的挂钩. |
| 19    | `termux_step_install_service_scripts` | yes | 为 termux-services 安装脚本 |
| 20    | `termux_step_install_license` | yes | 链接或复制特定包的许可证到`./share/doc/$TERMUX_PKG_NAME`. |
| 21    | `termux_step_extract_into_massagedir` | 不使用`make_install` | 提取在 `$TERMUX_PREFIX` 中修改的文件. |
| 22    | `termux_step_massage` | no | 剥离二进制文件，删除不需要的文件. |
| 22.1  | `termux_create_debian_subpackages` | no | 创建所有子包（debian 格式）. |
| 22.2  | `termux_create_pacman_subpackages` | no | 创建所有子包（pacman 格式）. |
| 24    | `termux_step_post_massage` | yes | 创建 `*.deb` 文件之前的最后一个钩子. |
| 25    | `termux_step_create_debian_package` | no | Create `*.deb` package. |
| 25.1  | `termux_step_create_pacman_package` | no | 创建 `*.pkg.tar.xz` 包. |
| 25.2  | `termux_step_create_debscripts` | yes | 创建维护者脚本，例如 安装前/安装后挂钩. |
| 25.3  | `termux_step_create_pacman_install_hook` | no | 将 `termux_step_create_debscripts` 的结果转换为 pacman 兼容的格式。 |
| 26    | `termux_step_finish_build` | no | 完成通知. |

##技巧和窍门
 
### 从本地 git 存储库构建

如果要从本地存储库构建包，可以设置
`TERMUX_PKG_SRCURL=file:///path/to/local/repo.git`。 文件夹名称
必须以 .git 结尾，所以如果你想构建
termux-api-package 运行：

```sh
git clone https://github.com/termux/termux-api-package termux-api-package.git
cd termux-api-package.git
# 进行更改，提交它们并可能创建一个标签
cd /path/to/termux-packages
# 编辑 packages/termux-api-build.sh, 设置:
#   `TERMUX_PKG_SRCURL=file:///path/to/termux-api-package.git`
# 您还需要更新 TERMUX_PKG_VERSION 以匹配您设置的标签。
# 如果你想从 master （或另一个分支）而不是你可以设置的标签构建
#   `TERMUX_PKG_GIT_BRANCH="master"`
./build-package.sh termux-api-package
```

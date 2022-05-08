## 结构

构建环境中可用的文件和目录:

- `./output` - 放置构建包的目录。默认不存在。

- `./disabled-packages` - 从主构建树中排除的包的目录。

- `./ndk-patches` - 带有 Android NDK sysroot 补丁的目录。

- `./packages` - 所有包（脚本和补丁）所在的目录。

- `./sample` - 用于创建新包的示例结构。

- `./scripts` - 构建系统的内部部分和一些实用程序脚本。

- `./build-all.sh` - 用于构建所有可用包的脚本。

- `./build-package.sh` - 用于构建一个或多个包的脚本。

- `./clean.sh` - 清理构建环境的脚本。

## 配置

### Docker容器

使用官方 Docker 镜像是构建 Termux 包的最佳方式，它确保您拥有与 Termux 拥有相同的环境，因此构建是可重复的。
如果你已经安装并运行了 Docker，那么启动构建环境应该和运行一样简单。

如果你已经安装并运行了 Docker，那么启动构建环境应该和运行一样简单

```shell
./scripts/run-docker.sh
```

等到最新的镜像被下载，然后容器的 shell 提示应该出现：
该`termux-packages`文件夹将被挂载为`/home/builder/termux-packages`.

您可以通过将命令作为参数提供给`./scripts/run-docker.sh`. 例子：
```shell
./scripts/run-docker.sh ./build-package.sh libandroid-support
```

有时应该更新 Docker 镜像。以下命令将更新镜像并删除过时的容器：

```shell
./scripts/update-docker.sh
```

#### 构建自己的 Docker 镜像

`Dockerfile`位于[目录](https://github.com/termux/termux-packages/blob/master/scripts/Dockerfile) 中`./scripts/`并配置为构建基于 Ubuntu 的镜像。

要构建 Docker 镜像，请执行以下命令：

要构建 Docker 映像，请执行以下命令：
```shell
cd ./scripts
docker build -t termux/package-builder .
```

如果您收到类似的错误：
```shell
-bash: /tmp/setup-ubuntu.sh: Permission denied.
```
请确保所有`*.sh`文件都具有权限`755`并且`umask`是 `0022`。

#### 在Windows上的Docker

对于 Windows 用户，可以使用 PowerShell 脚本：
```
.\scripts\run-docker.ps1
```

#### 提示

1. 多个容器

  您可以让多个容器一起运行。只需将不同的容器名称设置为环境变量：

   ```
   CONTAINER_NAME=termux_builder_2 ./scripts/run-docker.sh
   ```

2. 使用非官方的 Docker 镜像

您可以使用自定义 Docker 镜像作为环境变量`TERMUX_BUILDER_IMAGE_NAME`
但也建议为容器设置自定义名称（如上）：
   ```
   export TERMUX_BUILDER_IMAGE_NAME=username/termux-custom-builder
   export CONTAINER_NAME=custom-builder1
   ./scripts/run-docker.sh
   ```

### VirtualBox(VBox)

有一个[Vagrantfile](https://github.com/termux/termux-packages/blob/master/scripts/Vagrantfile) 用于设置 VirtualBox Ubuntu的安装。

- 运行 `vagrant plugin install vagrant-disksize` 为 Vagrant 安装 disksize 插件

- 运行 `cd scripts && vagrant up` 设置并启动虚拟机

- 运行 `vagrant ssh` 连接到虚拟机

### 主机操作系统

我们有脚本可以自动安装构建过程中使用的包。目前仅支持 Ubuntu 和 Arch Linux 发行版。请注意，所有脚本都需要`sudo`.

Ubuntu:
```
./scripts/setup-ubuntu.sh
```

Arch Linux:
```
./scripts/setup-archlinux.sh
```

此外，您还需要安装 Android SDK 和 NDK。默认情况下，SDK 和 NDK 都需要安装在`$HOME/lib`中，因此建议使用以下脚本正确设置它们：
```
./scripts/setup-android-sdk.sh
```

## Termux

我们的构建系统可用于在设备上构建包。尽管有一些限制，因为我们的构建脚本希望在运行时使用的实用程序在 Termux PREFIX (sysroot) 之外可用。

**Warning:** 不支持的设备`termux-exec`不工作！

如果您希望使用 Termux 安装，请在克隆`termux-package`存储库的根目录中执行以下命令：

```
./scripts/setup-termux.sh
```

请注意，在构建过程中生成的文件会安装到包管理器中`$PREFIX`，并且不会被包管理器跟踪。
**强烈建议在构建任何包之前`$HOME` 和之前备份`$PREFIX`！**

## 更改默认行为

以下环境变量会影响 Termux 构建系统的行为：

- `TERMUX_TOPDIR` - 指定 Termux 构建环境的基本目录。独立工具链、下载的源代码、包构建目录将在此处创建。默认为`$HOME/.termux-build`。

- `TERMUX_MAKE_PROCESSES` - 指定应由实用程序产生的进程数`make`。默认是 command 的输出`nproc`。

- `TERMUX_PKG_API_LEVEL` - 指定编译包的 Android API 级别。分支“master”的默认值为 24，分支“android-5”的默认值为 21。

- `TERMUX_PKG_MAINTAINER` - 指定应该写入包的维护者字段的值。默认为`@termux`。

- `TERMUX_PACKAGES_DIRECTORIES` - 指定包的根目录。默认为`packages`。

这些变量被以下参数 [覆盖](https://github.com/termux/termux-packages/wiki/Building-packages#build-options) `./build-package.sh`:

- `TERMUX_ARCH` - 指定应交叉编译的包的 CPU 架构。默认为`aarch64`。在设备上构建时无法更改。

- `TERMUX_CONTINUE_BUILD` - 如果设置，构建将跳过源提取和配置步骤并直接进入 termux_step_make

- `TERMUX_DEBUG_BUILD` - 执行调试构建。调试信息不​​会从二进制文件中删除，编译器优化将被关闭。

- `TERMUX_INSTALL_DEPS` - 如果设置为`true`，则依赖项将从 APT 存储库安装而不是构建。

- `TERMUX_NO_CLEAN` - 与 `TERMUX_INSTALL_DEPS`一起使用的特殊选项。如果设置为`true`，则在提取依赖项之前不会擦除 Termux 系统根目录。在设备上构建时，此变量应始终为`true`.

- `TERMUX_QUIET_BUILD` - 如果设置，附加参数将传递给实用程序 `make`以减少编译过程中的输出。

- `TERMUX_SKIP_DEPCHECK` - 如果设置，则不会进行依赖检查。

- `TERMUX_DEBDIR` - 放置`*.deb`的目录的路径。

可以通过 file 设置环境变量的自定义默认值：
`./scripts/properties.sh`.

## 关于 Termux PREFIX的重要说明

在 Termux 中，PREFIX相当于 Linux 发行版上的rootfs。

如果你决定改变`$TERMUX_PREFIX`，你就靠自己了。不可能混合来自不同前缀的包，因为更改一个假定编译的包永远不会在 Termux 环境中使用。

那些决定改变的人`$TERMUX_PREFIX`也应该改变`$TERMUX_ANDROID_HOME` 以避免路径问题。
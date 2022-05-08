## 实用程序

-`./build-all.sh`：
  以正确的顺序构建所有包（使用`./scripts/buildorder.py`）。

-`./clean.sh`：
  清理构建环境。

-`./scripts/check-built-packages.py`：
  比较本地 (git) 和远程 (apt) 软件包版本。

-`./scripts/check-pie.sh`：
  验证所有二进制文件都使用 PIE，这是 Android 5+ 所必需的。

-`./scripts/check-versions.sh`：
  检查软件包更新。

-`./scripts/ldd`：
  打印给定二进制文件所需的共享库列表。

-`./scripts/lint-packages.sh`：
  对 `build.sh` 脚本中的问题进行基本检查。

-`./scripts/list-packages.sh`：
  用一行摘要列出所有包。

-`./scripts/generate-bootstraps.sh`：
  从已发布的 apt repo 为 Termux 应用程序生成引导存档。

-`./scripts/build-bootstraps.sh`：
  从本地包源为 Termux 应用程序构建引导存档。

## 生成引导存档

Bootstrap 档案是一个带有基本包的 `$TERMUX_PREFIX` 的 zip 档案
预安装并用于在首次启动时创建最小的工作环境
Termux 应用程序。

通常，引导 zip 存档是通过执行 [`generate-bootstraps.sh`](https://github.com/termux/termux-packages/blob/master/scripts/generate-bootstraps.sh) 脚本生成的：
```
./scripts/generate-bootstraps.sh
```
构建的档案将在当前工作目录中可用。

请注意，您必须在 Web 服务器上部署一个 `apt` 存储库
为了能够生成引导档案。

默认脚本配置是
```
架构：aarch64 arm i686 x86_64
存储库 URL：https://packages-cf.termux.org/apt/termux-main/
```
因此，如果您想为自定义存储库生成引导程序，请考虑使用
以下命令行选项：

- `--architectures` - 以逗号分隔的引导架构列表
  应该生成。

- `--repository` - 你的 APT 存储库的 URL。离线引导生成不是
  支持的。


## 建立引导档案

[`build-bootstraps.sh`](https://github.com/termux/termux-packages/blob/master/scripts/build-bootstraps.sh) 是为`termux-app 构建引导档案的脚本` 来自本地包源，而不是像 `generate-bootstrap.sh` 那样在 apt repo 中发布的 debs。它允许为（分叉的）termux 应用程序轻松构建引导存档，而无需先发布 apt repo。运行`build-bootstrap.sh --help`以获取更多信息。

构建引导程序的包名称/前缀由“scrips/properties.sh”中的“TERMUX_APP_PACKAGE”定义。它默认为“com.termux”。如果包名称被更改，请确保运行 `./scripts/run-docker.sh ./clean.sh` 或传递 `-f` 以强制重建包。

＃##例子

为所有受支持的架构构建默认引导存档：
`./scripts/run-docker.sh ./scripts/build-bootstraps.sh &> build.log`

仅为 aarch64 构建默认引导存档：
`./scripts/run-docker.sh ./scripts/build-bootstraps.sh --architectures aarch64 &> build.log`

使用附加的 openssh 包构建引导存档，仅适用于 aarch64 架构：
`./scripts/run-docker.sh ./scripts/build-bootstraps.sh --architectures aarch64 --add openssh &> build.log`


## 构建 Termux 应用程序

在构建应用程序时下载发布[`termux-app/app/build.gradle`](https://github.com/termux/termux-app/blob/v0.118.0/app/build.gradle#L196)的[引导存档](https://github.com/termux/termux-packages/actions/workflows/bootstrap_archives.yml)并验证校验和。Generate bootstrap archivestermux-packages

如果您想使用自定义引导构建构建 `termux-app`，请在 Android Studio 中运行 `Build` -> `Clean Project`，然后将构建的引导 `zips` 替换/放置在 `termux-app/app/src/main/cpp`。在 termux 中的 [`downloadBootstrap()`](https://github.com/termux/termux-app/blob/v0.118.0/app/build.gradle#L196)函数的第一行添加 `return` 语句以防止函数运行，这样自定义的 `bootstrap-*.zip` 文件不会被下载的文件替换，然后将项目与 gradle 文件同步并构建 apk。
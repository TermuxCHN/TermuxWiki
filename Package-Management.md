# 包管理

检查[包管理维基页面](https://wiki.termux.com/wiki/Package_Management) 有关Termux内包管理的更多详细信息。

### 包命令错误

最近 Termux 将主要的 Termux 包存储库托管从 [**Bintray to Mirrors**](https://github.com/termux/termux-packages/issues/6348) 自从[**Bintray停止维护May 1st, 2021**](https://jfrog.com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/). 这给用户在使用 `pkg` 或 `apt` 运行软件包安装和更新命令时产生了问题，他们的命令将失败并出现类似于以下的错误：

```
E: The repository 'https://termux.org/packages stable Release' does no longer have a Release file.
N: Metadata integrity can't be verified, repository is disabled now.
N: Possible cause: repository is under maintenance or down (wrong sources.list URL?).
```

```
E: The repository 'https://dl.bintray.com/grimler/game-packages-24 games Release' does not have a Release file.
N: Metadata integrity can't be verified, repository is disabled now.
N: Possible cause: repository is under maintenance or down (wrong sources.list URL?).
```

```
E: The repository 'https://science.termux-mirror.ml science Release' does not have a Release file.
N: Metadata integrity can't be verified, repository is disabled now.
N: Possible cause: repository is under maintenance or down (wrong sources.list URL?).
```

如果是上面的情况，那么运行`termux-change-repo` 命令并且**更换你的源** 对于`main`存储库到不同的 [Termux镜像](https://github.com/termux/termux-packages/wiki/Mirrors). 如果你已经安装[`别的包存储库`](https://github.com/termux/termux-packages/wiki#packages)，如 `science`、`games` 和 `unstable`，那么您**必须** 换源。 您可以通过运行 `termux-info` 命令检查当前镜像。

有关如何更换后视镜的**分步说明**，请查看[这里](https://github.com/termux/termux-packages/issues/6726).

如果您在更改镜像后收到类似 `...Release' 将其 'Origin' 值从 'Bintray' 更改为 ...` 之类的错误，则[接受他们](https://github.com/termux/termux-packages/issues/6455).

更换镜像后，**强烈建议**运行 `pkg upgrade` 命令将所有软件包更新到最新的可用版本，或者至少使用 `pkg install termux-tools` 命令更新 `termux-tools` 软件包。 还要确保您的设备具有互联网连接，并且可以在浏览器中访问存储库 URL。

如果由于某种原因 `termux-change-repo` 不可用，您可以手动编辑 `sources.list` 以将 `main` url 替换为从[Termux镜像列表](https://github.com/termux/termux-packages/wiki/Mirrors)。 运行`nano $PREFIX/etc/apt/sources.list`编辑Termux源。这不会更改 `其他包存储库` 的 url，以更改之后运行 `pkg install termux-tools` 并使用 `termux-change-repo` 或手动编辑 `$PREFIX/etc/apt/sources.list.d` 目录下的文件。

如果用户仍在使用 `bintray` 作为镜像，或者有一段时间没有运行 `pkg upgrade` 命令来更新与 termux 包相关的脚本，则可能特别需要换源。
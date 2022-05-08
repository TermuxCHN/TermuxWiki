# 镜像维护者信息

不稳定包、游戏包和科学包已合并到主（termux-packages）存储库中，不再需要同步。

镜像 termux-packages、termux-root-packages 和 x11-packages 可以使用 rsync、[apt-mirror](https://github.com/apt-mirror/apt-mirror) 或 aptly 等多种工具完成。页面[如何镜像官方存储库]（https://github.com/termux/termux-packages/wiki/How-to-mirror-the-official-repositories）中概述了步骤。

# 存储库和镜像

Termux 包，除了引导环境，都是通过外部 Web 服务提供的。有一个主包服务器（种子）和编号或镜像，它们从中复制内容以减少负载并提供一定程度的冗余。镜像代表维护者、社区成员或非附属第三方运行，不能保证始终可用。

软件包需要在 Android 7.0 或更高版本上运行的 Termux。不要尝试使用旧环境中列出的存储库。

服务可用性可能因您的设备、互联网连接质量和您所在国家/地区的审查事件而异。如果我们发现我们这边存在严重问题，我们将在 GitHub 和我们的社区页面上发布公告。其他问题，例如在客户端或端点之间的某处引入，将被忽略。

## 如何使用

运行 `apt edit-sources`，注释掉现有 URL 并为选择的存储库添加行，或使用 `termux-tools` 包中的 `termux-change-repo` 脚本。

## 主要主机

可用镜像的默认 Termux 软件包存储库和内容播种器。服务器由 [FossHost](https://fosshost.org/) 免费提供 - 开源社区的托管服务提供商。

**服务器仅支持 IPv6，并使用同样由 FossHost 提供的 IPv6 到 IPv4 代理。它很慢，但我们目前没有更好的东西。希望你明白发生了什么。如果下载速度慢而困扰您，请改用镜像。**

| 存储库                                             | sources.list 内容                                            |
|:-------------------------------------------------------|:--------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://packages.termux.org/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://packages.termux.org/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://packages.termux.org/apt/termux-x11 x11 main`     |

CloudFlare CDN 端点。 快速且稳定，但对上传有限制（“免费”计划中每个 POST 最多 100MB），这使得无法使用它通过 GitHub Actions + Aptly REST API 提交包。

| 存储库                                             | sources.list 内容                                               |
|:-------------------------------------------------------|:-----------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://packages-cf.termux.org/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://packages-cf.termux.org/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://packages-cf.termux.org/apt/termux-x11 x11 main`     |

请不要在您的分叉中使用我们的主机。 建立自己的存储库。 否则考虑为我们的项目做出贡献，而不是维护自定义分支。

## 镜子

列出了所有已知的 Termux 镜像。 如果您托管了一个但未在列表中找到它，请在 https://github.com/termux/termux-packages/issues 中打开该问题。

### 镜像是 pkg 中镜像旋转的一部分

当 pkg 检查可用的镜像并选择一个时，可能会随机选择这些镜像（按字母顺序列出）。

#### [a1batross](https://github.com/a1batross) 的镜像

每天更新四次。

| 存储库                                             | sources.list 内容                                         |
|:-------------------------------------------------------|:-----------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://termux.mentality.rip/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://termux.mentality.rip/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://termux.mentality.rip/termux-x11 x11 main`     |

#### Mirrors by [Astra ISP](https://astra.in.ua/)

每 4 小时更新一次。

| 存储库                                             | sources.list 内容                                           |
|:-------------------------------------------------------|:-------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://termux.astra.in.ua/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://termux.astra.in.ua/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://termux.astra.in.ua/apt/termux-x11 x11 main`     |

#### Mirrors by [Grimler](https://github.com/grimler91)

从主节点镜像，在“办公时间”每小时更新一次（需要一个 gpg 硬件密钥才能访问 repo 签名才能工作）。

| 存储库                                             | sources.list 内容                                      |
|:-------------------------------------------------------|:--------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://grimler.se/termux/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://grimler.se/termux/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://grimler.se/termux/termux-x11 x11 main`     |

#### Mirrors by [Librehat](https://github.com/librehat)

每天更新四次。

| 存储库                                             | sources.list 内容                                            |
|:-------------------------------------------------------|:--------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://termux.librehat.com/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://termux.librehat.com/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://termux.librehat.com/apt/termux-x11 x11 main`     |

#### Mirrors by [mwt](https://github.com/mwt)

在美国新泽西州。 每天更新四次。

| 存储库                                             | sources.list 内容                                  |
|:-------------------------------------------------------|:----------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirror.mwt.me/termux/main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirror.mwt.me/termux/root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirror.mwt.me/termux/x11 x11 main`     |

#### Mirrors by [普渡大学](https://plug-mirror.rcac.purdue.edu/info.html)

在美国印第安纳州。 每天更新四次。

| 存储库                                             | sources.list 内容                                                       |
|:-------------------------------------------------------|:-------------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://plug-mirror.rcac.purdue.edu/termux/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://plug-mirror.rcac.purdue.edu/termux/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://plug-mirror.rcac.purdue.edu/termux/termux-x11 x11 main`     |

#### Mirrors by [Sahilister](https://github.com/sahilister)

每天更新四次。

| 存储库                                             | sources.list 内容                                             |
|:-------------------------------------------------------|:---------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://termux.sahilister.in/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://termux.sahilister.in/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://termux.sahilister.in/apt/termux-x11 x11 main`     |


### 伊朗镜像

为伊朗用户提供镜像，以提高 ping 和下载速度。

#### Mirrors by [Bardia Moshiri](https://bardia.tech/)

这个镜像站托管在伊朗。 每天更新四次。

rsync：`rsync://mirror.bardia.tech/termux`

联系方式：`fakeshell@bardia.tech`

| 存储库                                             | sources.list 内容                                                          |
|:-------------------------------------------------------|:----------------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirror.bardia.tech/termux/termux-packages-24 stable main`      |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirror.bardia.tech/termux/termux-root-packages-24 root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirror.bardia.tech/termux/x11-packages x11 main`               |

### 中国镜像

为中国用户提供镜像，以提高 ping 和下载速度。

#### Mirrors by [北京外国语大学](http://www.bfsu.edu.cn/)

| 存储库                                             | sources.list 内容                                                   |
|:-------------------------------------------------------|:---------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.bfsu.edu.cn/termux/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.bfsu.edu.cn/termux/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.bfsu.edu.cn/termux/apt/termux-x11 x11 main`     |

#### Mirrors by [重庆邮电大学](https://www.cqupt.edu.cn/)

更多信息请访问 https://mirrors.cqupt.edu.cn/。

| 存储库                                             | sources.list 内容                                                    |
|:-------------------------------------------------------|:----------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.cqupt.edu.cn/termux/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.cqupt.edu.cn/termux/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.cqupt.edu.cn/termux/apt/termux-x11 x11 main`     |

#### Mirrors by [东莞理工学院](https://www.dgut.edu.cn/)

| 存储库                                             | sources.list 内容                                                   |
|:-------------------------------------------------------|:---------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.dgut.edu.cn/termux/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.dgut.edu.cn/termux/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.dgut.edu.cn/termux/apt/termux-x11 x11 main`     |

#### Mirrors by [哈尔滨工业大学](https://www.hit.edu.cn/)

| 存储库                                             | sources.list 内容                                                  |
|:-------------------------------------------------------|:--------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.hit.edu.cn/termux/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.hit.edu.cn/termux/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.hit.edu.cn/termux/apt/termux-x11 x11 main`     |

#### Mirrors by [eScience中心,南京大学](https://www.nju.edu.cn/)

| 存储库                                             | sources.list 内容                                                 |
|:-------------------------------------------------------|:-------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirror.nju.edu.cn/termux/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirror.nju.edu.cn/termux/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirror.nju.edu.cn/termux/apt/termux-x11 x11 main`     |

#### Mirrors by [清华大学](https://tuna.moe/)

| 存储库                                             | sources.list 内容                                                            |
|:-------------------------------------------------------|:------------------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-main stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-root root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-x11 x11 main`     |

#### Mirrors by [中国科学技术大学](https://lug.ustc.edu.cn/)

| 存储库                                             | sources.list 内容                                                    |
|:-------------------------------------------------------|:----------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.ustc.edu.cn/termux/apt/termux-main/ stable main` |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.ustc.edu.cn/termux/apt/termux-root/ root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.ustc.edu.cn/termux/apt/termux-x11/ x11 main`     |

#### Mirrors by [阿里云](https://mirrors.aliyun.com/)

更多信息请访问：https://mirrors.aliyun.com/about.

| 存储库                                             | sources.list 内容                                                          |
|:-------------------------------------------------------|:----------------------------------------------------------------------------|
| [Main](https://github.com/termux/termux-packages)      | `deb https://mirrors.aliyun.com/termux/termux-packages-24 stable main`      |
| [Root](https://github.com/termux/termux-root-packages) | `deb https://mirrors.aliyun.com/termux/termux-root-packages-24 root stable` |
| [X11](https://github.com/termux/x11-packages)          | `deb https://mirrors.aliyun.com/termux/x11-packages x11 main`               |

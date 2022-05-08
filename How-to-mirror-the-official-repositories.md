# 镜像

此页面包含有关如何镜像/同步官方存储库的建议，或者创建一个新的公共 termux 镜像，或者只是拥有一个本地存储库。

## 使用 rsync 进行镜像

无法从托管在 fosshost、packages.termux.org 的主存储库使用 rsync 进行镜像，因为它们的代理仅在标准端口 22、80、443 上转发流量。尽管可以从 grimler.se 的辅助主机进行镜像 . 使用用户名 rsync 和密码 termuxmirror 完成身份验证，因此要将所有镜像同步到本地目录中的文件夹 `termux`，请执行以下操作：

```
RSYNC_PASSWORD=termuxmirror rsync -a --delete rsync@grimler.se::termux termux
```

或仅同步主存储库：

```
RSYNC_PASSWORD=termuxmirror rsync -a --delete --exclude termux-root --exclude termux-x11 rsync@grimler.se::termux termux
```
## 使用 apt-mirror 进行镜像

apt-mirror 是一个 perl 脚本，有几个依赖项需要通过 cpan 或类似工具安装。 安装后，需要一个配置文件来选择要同步的存储库。 这是一个示例配置文件，它将所有 termux 的 repos 同步到`/data/data/com.termux/files/home/termux`：

```
set base_path         /data/data/com.termux/files/home/termux
set mirror_path       $base_path/mirror
set skel_path         $base_path/skel
set var_path          $base_path/var
set postmirror_script $var_path/postmirror.sh
set defaultarch       aarch64
set run_postmirror    0
set nthreads          20
set limit_rate        100m
set _tilde            0
# Use --unlink with wget (for use with hardlinked directories)
set unlink            0
set use_proxy         off
set http_proxy        127.0.0.1:3128
set proxy_user        user
set proxy_password    password

# Termux主存储库
deb-aarch64           https://packages.termux.org/apt/termux-main stable main
deb-arm               https://packages.termux.org/apt/termux-main stable main
deb-i686              https://packages.termux.org/apt/termux-main stable main
deb-x86_64            https://packages.termux.org/apt/termux-main stable main
clean                 https://packages.termux.org/apt/termux-main

# root存储库
deb-aarch64           https://packages.termux.org/apt/termux-root root stable
deb-arm               https://packages.termux.org/apt/termux-root root stable
deb-i686              https://packages.termux.org/apt/termux-root root stable
deb-x86_64            https://packages.termux.org/apt/termux-root root stable
clean                 https://packages.termux.org/apt/termux-root

# x11存储库
deb-aarch64           https://packages.termux.org/apt/termux-x11 x11 main
deb-arm               https://packages.termux.org/apt/termux-x11 x11 main
deb-i686              https://packages.termux.org/apt/termux-x11 x11 main
deb-x86_64            https://packages.termux.org/apt/termux-x11 x11 main
clean                 https://packages.termux.org/apt/termux-x11
```

要开始同步，请运行 apt-mirror /path/to/termux-config-from-above.list 。

## 恰当地镜像

在处理包和多个存储库时，Aptly 很方便。 但是，您需要在发布时使用自己的 gpg 密钥对 repo 进行签名，这意味着其他人无法订阅和使用您的镜像，除非他们将您的 gpg 密钥添加到 apt 的受信任密钥中。 不过，个人使用的镜像效果很好，可以通过以下方式完成：

```
gpg --no-default-keyring --keyring trustedkeys.gpg --recv-keys 5A897D96E57CF20C
aptly mirror create termux-main https://packages.termux.org/apt/termux-main stable
aptly mirror create termux-root https://packages.termux.org/apt/termux-root root
aptly mirror create termux-x11 https://packages.termux.org/apt/termux-x11 x11

aptly mirror update termux-main
aptly mirror update termux-root
aptly mirror update termux-x11

DATE=$(date +%Y%m%d_%H%M)
aptly snapshot create termux-main-$DATE from mirror termux-main
aptly snapshot create termux-root-$DATE from mirror termux-root
aptly snapshot create termux-x11-$DATE from mirror termux-x11

aptly publish snapshot termux-main-$DATE
aptly publish snapshot termux-root-$DATE
aptly publish snapshot termux-x11-$DATE
```

要稍后更新您的镜像，请重复除“aptly mirror create”之外的所有步骤。

# Haskell 包指南

## 包装政策

对于 Haskell 包，我们遵循 [ArchLinux 的政策](https://wiki.archlinux.org/title/Haskell_package_guidelines)
默认情况下只提供动态库和可执行文件，除了没有单独的 ghc-pkg 数据库
静态包。

由于静态包没有单独的 ghc-pkg 数据库，因此安装和使用静态库应该可以工作
开箱即用，无需用户方面的任何额外工作。

另见：https://wiki.archlinux.org/title/Haskell

## 构建

尽管 Termux 构建系统可以自动构建 Haskell 包，但仍然可以使用以下参数来覆盖默认值。

### 变量

以下变量存储要传递给 `termux-ghc-setup` 的标志（如果覆盖构建步骤，请指定它们）：

- `TERMUX_HASKELL_LLVM_BACKEND` 存储标志以启用除 `i686` 之外的拱门的 LLVM 后端。
- `TERMUX_HASKELL_OPTIMISATION` 根据 `TERMUX_DEBUG_BUILD` 是 `true` 还是 `false` 存储包优化级别的标志。

### 工具

GHC 交叉编译器 bin 如下：

| 原始名称 | 别名（在构建期间） |
| ------------- | -------------------------- |
| ghc           | `termux-ghc`               |
| ghc-pkg       | `termux-ghc-pkg`           |
| hsc2hs        | `termux-hsc2hs`            |
| hp2ps         | `termux-hp2ps`             |

- 为了节省空间并避免在必要时为主机安装 ghc，我们提供了预编译的 `Setup.hs` 脚本作为 `termux-ghc-setup`。
- 它可以处理 `simple`、`configure` 和 `make` 目标。 对于 `custom` 目标，我们为主机安装 ghc，编译
   `Setup.hs` 并使其作为 `termux-ghc-setup` 可用。

## 备注：

- 不要使用`runhaskell Setup`。 它的所有功能都由 `termux-ghc-setup` 提供。
- 相同的命名方案用于设备上构建的 ghc 工具。 因此，您不必为它处理任何特定的事情。
- 目前我们无法使用 `template-haskell` 编译包。
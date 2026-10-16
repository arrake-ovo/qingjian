# Colemak 微软双拼定制版

本 fork 由 arrake-ovo 维护，基于上游 [qingjian-team/qingjian](https://github.com/qingjian-team/qingjian) 的提交 `db9f31871487404cb586ef07fad56b9d37bd4274`。它保留上游许可证及署名，是个人定制版本；上游官方发布与本 fork 的发布分别维护。

## 改了什么

- 新增 `colemak_mspy`，偏好设置中显示「微软双拼（Colemak）」，对应个人 Rime `colemak_custom_mspy`。
- 修正没有生成词库时的打包回退：优先带完整 lexicon，而非仅示例词库。
- 兼容新版 pkgbuild 分析结果省略重定位字段的情况，确保安装位置固定。
- 本次发布版本为 `0.1.3-colemak.1`，仅提供 macOS Apple Silicon（arm64）编译包；没有构建 Intel 或 Windows 包。

声母仍是 `v/i/u = zh/ch/sh`，韵母与标准微软双拼的主要差异如下：

| 键 | 本方案韵母 |
|---|---|
| r | iong、ong |
| t | en |
| y | ing |
| p | uan、er |
| s | iang、uang |
| d | eng |
| f | ao |
| g | ue |
| j | uai、ü |
| k | in |
| ; | un |
| v | ui、ve |
| n | an |

例如 `nihf` → `ni'hao`（你好）、`x;` → `xun`、`xg` → `xue`；零声母 ao 用 `of` 或 `af`。完整表在 `crates/qingjian-core/src/shuangpin/table.rs`。

## 下载与安装

从 [本 fork 的 Release](https://github.com/arrake-ovo/qingjian/releases/tag/colemak-macos-v0.1.3-colemak.1) 下载 `Qingjian-0.1.3-colemak.1-arm64.pkg`，并用随附的 `SHA256SUMS` 核验。安装器声明 macOS 13 或更新版本，运行需要 Apple Silicon；本次构建验证环境和工具链记录在 `build-info.json`。

安装包把应用装到 `/Library/Input Methods/Qingjian.app`。如果已有 `~/Library/Input Methods/Qingjian.app` 开发版，请先按 [macOS 卸载说明](../../apps/macos/README.md) 移除旧副本，保留用户配置与学习数据，避免同一 bundle ID 的两份应用互相冲突。

应用使用 ad-hoc 签名，安装包未经过 Developer ID 签名和 Apple 公证。macOS 如拦截安装，请在「系统设置 → 隐私与安全性」按系统提示允许；不要关闭整个系统的安全检查。

安装后在偏好设置中选择「微软双拼（Colemak）」，或在 `~/Library/Application Support/Qingjian/config.toml` 中设置：

```toml
[general]
shuangpin = "colemak_mspy"
```

## 与 nix_dotfiles 的关系

[arrake-ovo/nix_dotfiles](https://github.com/arrake-ovo/nix_dotfiles) 管理 `config.toml`、自定义短语和输入源注册设置，通过 Home Manager 创建指向仓库文件的软链接。它目前不安装青简应用；换机器时需先安装这里的定制包。API 密钥、学习数据和输入日志不包含在发布包中。

## 重新构建

```sh
git clone --branch codex/colemak-mspy https://github.com/arrake-ovo/qingjian.git
cd qingjian
git checkout colemak-macos-v0.1.3-colemak.1
tools/release/data-fetch.sh
cargo test --workspace --locked
apps/macos/scripts/bundle.sh --pkg
```

工具链由 `rust-toolchain.toml` 固定。产品数据下载自上游，按 `tools/release/data.lock` 校验，包内只包含应用和上游发布数据。源码 tag 对应本次二进制的构建提交；实际包的校验和以 Release 附件为准。

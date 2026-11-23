# MDCx-Rust 安装与使用

[Windows](#windows-桌面版) · [macOS](#macos-桌面版) · [Linux 桌面](#linux-桌面版) · [CLI＋Web](#linux-cliweb) · [Docker](#docker) · [数据目录](#数据目录)

从 [最新发布](https://github.com/xiarenyyds/MDCx-Rust/releases/latest) 下载对应平台的压缩包。解压后进入与压缩包同名的文件夹，运行其中同名程序，名称及大小写一致；Windows 为 `.exe`，macOS 为 `.app`。首次运行在该文件夹内创建 `data` 目录，移动时将整个文件夹一起移动。

## Windows 桌面版

适用于 Windows x64。解压后进入与压缩包同名的文件夹，运行其中同名的 `.exe`；运行依赖自动释放到该目录下 `data` 内的隐藏目录。
原生 Cookie 登录窗口需要 Microsoft Edge WebView2 Runtime。

## macOS 桌面版

提供 Apple 芯片（arm64）和 Intel 芯片（x64）两个安装包，最低 macOS 12。按设备芯片选择安装包，解压后进入同名文件夹，启动其中同名 `.app`。首次运行在该文件夹内的 `.app` 旁创建 `data`，配置位于 `data/config`，运行依赖保留在 `.app` 内。移动时将整个文件夹一起移动。
当前使用临时签名，未进行 Apple 公证；若系统阻止打开，核对下载来源后在“系统设置 → 隐私与安全性”允许打开。

## Linux 桌面版

适用于 Ubuntu 22.04 及兼容的较新 x64 Linux 桌面系统。解压后进入与压缩包同名的文件夹，启动其中同名程序，运行依赖自动释放到该目录下 `data` 内的隐藏目录。
Ubuntu/Debian 可安装所需运行库：

```sh
sudo apt-get install libgtk-3-0 libwebkit2gtk-4.1-0 libayatana-appindicator3-1 libglu1-mesa ca-certificates
```

## Linux CLI＋Web

提供 x64 和 ARM64 两种版本，适用于 glibc Linux（ARM64 最低 Debian 12 或同等运行库）。解压后进入与压缩包同名的文件夹，执行：

```sh
# 在解压后的文件夹内匹配当前版本、当前架构的唯一程序。
./MDCx-Rust-*-linux-*-server
```

打开 `http://127.0.0.1:8080`，访问令牌读取 `data/runtime/server-token`。
后台持续在终端运行，关闭网页不会结束任务；按 Ctrl+C 停止后台。
网页与 FFprobe 封装在程序内，首次运行自动释放到 `data` 内的隐藏目录，无需安装桌面环境。

| 环境变量 | 用途 |
| --- | --- |
| `MDCX_RUST_DATA_DIR` | 数据根目录，默认安装目录下 `data`；配置位于其 `config` 子目录 |
| `MDCX_BIND` | 监听地址，默认 `127.0.0.1:8080`，局域网访问可设 `0.0.0.0:8080` |
| `MDCX_SERVER_TOKEN` | 自定义访问令牌，至少 16 字符；未设置时自动生成 |

媒体目录直接在软件中选择，无需额外声明；目录需具有相应的读写权限。后台以服务方式运行时，请保持环境变量与配置目录固定。

## Docker

适合在 NAS 或服务器持续运行，通过浏览器使用。镜像包含后台、网页和 FFprobe，支持 AMD64、ARM64，Docker 自动选择对应架构。

使用 `latest` 跟随最新正式版：

```sh
docker pull xrbzy/mdcx-rust:latest
```

完整 Compose、挂载目录、权限、访问令牌及媒体助手连接步骤统一见 [Docker 部署](DOCKER.md)。

## 数据目录

桌面与 CLI 默认数据目录均为程序旁的 `data`，macOS 为 `.app` 旁，配置在 `data/config`。可通过 `MDCX_RUST_DATA_DIR` 指定其他数据目录。

| 资料 | 位置与用途 |
| --- | --- |
| 全局设置 | `data/config/global.json`，保存所有方案共用的设置 |
| 刮削方案 | `data/config/config.json` 或 `data/config/方案名.json` |
| 数据库 | `data/database/`，保存影片离线资料、演员及映射等资料 |
| 日志 | `data/logs/`，按用途分类 |
| 运行资料 | `data/runtime/`，包含任务、断点及服务端访问令牌 |

## 下载文件校验

Linux 可在安装包与 `SHA256SUMS.txt` 所在目录运行 `sha256sum -c SHA256SUMS.txt`；macOS 使用 `shasum -a 256 -c SHA256SUMS.txt`。
Windows 可用 PowerShell 的 `Get-FileHash 文件名 -Algorithm SHA256` 与清单对照。

## 安装后的操作

完成首次刮削、选择模式、分享配置、使用离线资料以及配置演员管理和通知，见[功能与使用](USAGE.md)。Emby 插件单独从 [MediaAssistant](https://github.com/xiarenyyds/MediaAssistant) 下载并按其文档安装。

[返回首页](../README.md) · [功能与使用](USAGE.md) · [Docker 部署](DOCKER.md) · [更新日志](https://github.com/xiarenyyds/MDCx-Rust/releases)

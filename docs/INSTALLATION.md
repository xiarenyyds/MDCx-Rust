# MDCx-Rust 安装与使用

从 [最新发布](https://github.com/xiarenyyds/MDCx-Rust/releases/latest) 下载对应平台的完整压缩包，解压后保留内部目录结构。

## Windows 桌面版

适用于 Windows x64。解压后运行 `mdcx-rust.exe`，不要单独移动 EXE 或删除旁边的 DLL、data 等目录。
原生 Cookie 登录窗口需要 Microsoft Edge WebView2 Runtime。

## macOS 桌面版

提供 Apple 芯片（arm64）和 Intel 芯片（x64）两个安装包，最低 macOS 12。按设备芯片选择安装包，解压后将 `mdcx-rust.app` 移入“应用程序”再启动。
当前使用临时签名，未进行 Apple 公证；若系统阻止打开，核对下载来源后在“系统设置 → 隐私与安全性”允许打开。

## Linux 桌面版

适用于 Ubuntu 22.04 及兼容的较新 x64 Linux 桌面系统。解压后在 `bundle` 目录运行 `./mdcx-rust`。
Ubuntu/Debian 可安装所需运行库：

```sh
sudo apt-get install libgtk-3-0 libwebkit2gtk-4.1-0 libayatana-appindicator3-1 libglu1-mesa ca-certificates
```

## Linux CLI＋Web

提供 x64 和 ARM64 两种版本，适用于 glibc Linux（ARM64 最低 Debian 12 或同等运行库）。解压后进入安装目录，执行：

```sh
MDCX_MEDIA_ROOTS='/实际媒体目录' sh start.sh
```

打开 `http://127.0.0.1:8080`，访问令牌读取 `data/runtime/server-token`。
后台持续在终端运行，关闭网页不会结束任务；按 Ctrl+C 停止后台。
包内包含网页与 FFprobe，无需安装桌面环境。

| 环境变量 | 用途 |
| --- | --- |
| `MDCX_MEDIA_ROOTS` | 可操作的媒体根目录，多个目录用分号分隔，须已存在 |
| `MDCX_RUST_DATA_DIR` | 配置目录，默认安装目录下 `data` |
| `MDCX_BIND` | 监听地址，默认 `127.0.0.1:8080`，局域网访问可设 `0.0.0.0:8080` |
| `MDCX_SERVER_TOKEN` | 自定义访问令牌，至少 16 字符；未设置时自动生成 |

媒体目录需有读写权限。后台以服务方式运行时，请保持环境变量与配置目录固定。

## Docker

镜像下载、完整部署和日常管理步骤见 [Docker 下载与使用](DOCKER.md)。

镜像 `xrbzy/mdcx-rust:0.1.1` 和 `latest` 支持 AMD64、ARM64，Docker 自动选择对应架构。镜像包含 CLI、网页和 FFprobe。

```sh
docker pull xrbzy/mdcx-rust:0.1.1
```

将以下内容保存为 `compose.yaml`，修改目录和令牌后执行 `docker compose up -d`：

```yaml
services:
  mdcx:
    image: xrbzy/mdcx-rust:0.1.1
    restart: unless-stopped
    ports:
      - "8080:8080"
      # 使用媒体助手时取消下一行注释。
      # - "13114:13114"
    environment:
      TZ: Asia/Shanghai
      MDCX_SERVER_TOKEN: "replace-with-your-random-token-at-least-16-characters"
    volumes:
      - /你的配置目录:/config
      - /你的媒体目录:/media
```

镜像默认以 UID/GID `1000:1000` 运行，请提前创建目录并确保该用户有读写权限；需要时可通过 Compose 的 `user: "UID:GID"` 指定实际用户。浏览器打开 `http://主机地址:8080`，输入设置的令牌。媒体路径填写 `/media/...`。

媒体助手需在网页中启用监听，监听地址设置为 `0.0.0.0`、端口设置为 `13114`，并映射同一端口。Emby 填写 Docker 主机可达的地址；媒体路径相同且 MDCx 可访问时无需路径映射，不同时再映射到 MDCx 容器内的对应目录（本例为 `/media`）。镜像采用精简运行环境，不包含 Shell；查看日志使用 `docker compose logs`。

## 首次使用

选择自己的媒体目录，按影片类型调整网站与字段优先级，按需填写代理、Cookie 和 API 令牌，然后保存设置。

桌面配置目录：macOS 为 `~/Library/Application Support/mdcx-rust`，Windows 为 `%APPDATA%/mdcx-rust`，Linux 为 `${XDG_DATA_HOME:-~/.local/share}/mdcx-rust`。

## Emby 插件

主菜单“媒体助手”右上角点击“下载插件”。将下载的 `MediaAssistant.dll` 放入 Emby 插件目录，重启 Emby。
插件连接地址、令牌与路径映射见 [插件安装说明](https://github.com/xiarenyyds/MediaAssistant/blob/main/docs/INSTALLATION.md)。

## 日志管理与停止任务

“日志管理”按系统、刮削、媒体助手、常用工具、封面洗版及 NFO 批量修改分类查看。后端日志按 20 MB 分卷，保留 7 天；“显示详细日志”切换技术细节。点击“打开”可在文件管理器定位日志文件。

停止刮削后不再派发新任务，已开始的影片会处理完成。媒体助手后端任务的失败原因和重试也在日志管理中查看。

## 数据库

在 Amazon ASIN、演员资料、演员映射或信息映射页面点击“下载”。数据库由 [独立仓库](https://github.com/xiarenyyds/databases) 提供，各页面的下载网址填写对应数据库文件的链接。

首次启动为空库；下载成功后完整替换对应本地库，本地新增、修改和删除结果也会被替换。下载或校验失败保留原库。演员和信息映射共用 `actor-mapping.sqlite3`。

## 下载文件校验

Linux 可在安装包与 `SHA256SUMS.txt` 所在目录运行 `sha256sum -c SHA256SUMS.txt`；macOS 使用 `shasum -a 256 -c SHA256SUMS.txt`。
Windows 可用 PowerShell 的 `Get-FileHash 文件名 -Algorithm SHA256` 与清单对照。

[返回首页](../README.md) · [更新日志](../CHANGELOG.md) · [TG 交流群](https://t.me/mdcx_rust)

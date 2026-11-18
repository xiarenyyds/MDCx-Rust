# MDCx-Rust

媒体资料刮削与管理工具。支持 33 个影片来源、速度优先和字段优先、离线资料库、NFO 编辑、封面裁剪与水印、标题和简介翻译、演员管理及 Emby 媒体助手。

[下载 MDCx-Rust](https://github.com/xiarenyyds/MDCx-Rust/releases/latest) · [Docker 镜像](https://hub.docker.com/r/xrbzy/mdcx-rust) · [安装与使用](docs/INSTALLATION.md) · [更新日志](CHANGELOG.md) · [TG 交流群](https://t.me/mdcx_rust)

## 下载

四端共六个压缩包，选择对应系统及芯片版本：

| 平台 | 文件 |
| --- | --- |
| Windows x64 桌面 | `MDCx-Rust-v0.1.2-windows-x64.zip` |
| macOS Apple 芯片 | `MDCx-Rust-v0.1.2-macos-arm64.zip` |
| macOS Intel | `MDCx-Rust-v0.1.2-macos-x64.zip` |
| Linux x64 桌面 | `MDCx-Rust-v0.1.2-linux-x64.tar.gz` |
| Linux x64 CLI＋Web | `MDCx-Rust-v0.1.2-linux-x64-server.tar.gz` |
| Linux ARM64 CLI＋Web | `MDCx-Rust-v0.1.2-linux-arm64-server.tar.gz` |

解压后保留完整目录结构。桌面版直接启动；CLI＋Web 版通过浏览器操作，关闭网页不会停止后端任务。发布同时提供 `SHA256SUMS.txt`。

## Docker 版

从 [Docker Hub · xrbzy/mdcx-rust](https://hub.docker.com/r/xrbzy/mdcx-rust) 下载，支持 AMD64、ARM64，自动匹配设备架构，包含 CLI 后台、Web 界面和 FFprobe。

```sh
docker pull xrbzy/mdcx-rust:0.1.2
```

也可使用 `latest` 跟随最新正式版。启动后通过浏览器访问 `http://主机地址:8080`；配置和媒体分别挂载到 `/config`、`/media`。完整的 Compose 示例、令牌和目录设置、Emby 连接及更新步骤见 [Docker 下载与使用](docs/DOCKER.md)。

## 首次使用

填写媒体目录，选择网站及优先级，按需配置代理、Cookie 和 API 令牌，再保存配置。首次启动的数据库为空，在数据管理页按需从 [databases](https://github.com/xiarenyyds/databases) 下载；完整下载会替换本地对应数据库。

## Emby 媒体助手

主程序不附带插件。在主菜单“媒体助手”右上角点击“下载插件”，或前往 [MediaAssistant](https://github.com/xiarenyyds/MediaAssistant) 单独下载。配置监听、令牌与路径映射，使用底部统一保存栏保存，再开始监听。任务记录位于“日志管理 → 媒体助手”。

使用说明和更新日志直接保存在本仓库，发布附件只放安装包和校验清单。

# MDCx-Rust

媒体资料刮削与管理工具。支持 33 个影片来源、速度优先和字段优先、离线资料库、NFO 编辑、封面裁剪与水印、标题和简介翻译、演员管理及 Emby 媒体助手。

[下载 MDCx-Rust](https://github.com/xiarenyyds/MDCx-Rust/releases/latest) · [Docker 镜像](https://hub.docker.com/r/xrbzy/mdcx-rust) · [安装与使用](docs/INSTALLATION.md) · [更新日志](https://github.com/xiarenyyds/MDCx-Rust/releases) · [TG 交流群](https://t.me/mdcx_rust)

## 下载

四端共六个压缩包，选择对应系统及芯片版本：

| 平台 | 文件 |
| --- | --- |
| Windows x64 桌面 | `MDCx-Rust-v0.1.4-windows-x64.zip` |
| macOS Apple 芯片 | `MDCx-Rust-v0.1.4-macos-arm64.zip` |
| macOS Intel | `MDCx-Rust-v0.1.4-macos-x64.zip` |
| Linux x64 桌面 | `MDCx-Rust-v0.1.4-linux-x64.tar.gz` |
| Linux x64 CLI＋Web | `MDCx-Rust-v0.1.4-linux-x64-server.tar.gz` |
| Linux ARM64 CLI＋Web | `MDCx-Rust-v0.1.4-linux-arm64-server.tar.gz` |

解压后进入与压缩包同名的文件夹，运行其中同名程序。首次启动在程序旁创建 `data`，配置位于 `data/config`；移动时将整个文件夹一起移动。CLI＋Web 版通过浏览器操作，关闭网页不会停止后端任务。发布同时提供 `SHA256SUMS.txt`。

## Docker 版

从 [Docker Hub · xrbzy/mdcx-rust](https://hub.docker.com/r/xrbzy/mdcx-rust) 下载，支持 AMD64、ARM64，自动匹配设备架构，包含 CLI 后台、Web 界面和 FFprobe。

```sh
docker pull xrbzy/mdcx-rust:0.1.4
```

也可使用 `latest` 跟随最新正式版。启动后通过浏览器访问 `http://主机地址:8080`；配置和媒体分别挂载到 `/data`、`/media`。完整的 Compose 示例、令牌和目录设置、Emby 连接及更新步骤见 [Docker 下载与使用](docs/DOCKER.md)。

## 首次使用

填写媒体目录，选择网站及优先级，按需配置代理、Cookie 和 API 令牌，再保存配置。首次启动的数据库为空，在数据管理页按需从 [databases](https://github.com/xiarenyyds/databases) 下载；完整下载会替换本地对应数据库。

## Emby 媒体助手

主程序不附带插件。在主菜单“媒体助手”右上角点击“下载插件”，或前往 [MediaAssistant](https://github.com/xiarenyyds/MediaAssistant) 单独下载。配置监听、令牌与路径映射，使用底部统一保存栏保存，再开始监听。任务记录位于“日志管理 → 媒体助手”。

使用说明保存在本仓库，各版本更新内容见 Releases 发布说明，发布附件只放安装包和校验清单。

## 致谢与上游项目

MDCx-Rust 是基于 [MDCx-diy](https://github.com/cdlongbow/mdcx-release) 的 Rust 重构版，使用 Rust 重构核心处理逻辑，并以 Flutter 构建跨平台界面。感谢 [cdlongbow](https://github.com/cdlongbow) 对 MDCx-diy 的持续开发与维护，以及该项目在功能设计、资料刮削和使用体验方面为本项目提供的基础与参考。

同时向 MDCx 的上游项目及历代开发者致敬：

- [yoshiko2/Movie_Data_Capture](https://github.com/yoshiko2/Movie_Data_Capture)：早期的命令行媒体资料刮削工具，为后续项目奠定基础。
- [moyy996/AVDC](https://github.com/moyy996/AVDC)：基于 Movie_Data_Capture 早期版本发展，使用 PyQt 提供图形界面。
- Hermit/MDCx：基于 AVDC 继续开发，曾由 anyabc 分发源码与可执行文件。
- [sqzw-x/mdcx](https://github.com/sqzw-x/mdcx)：基于 Hermit/MDCx 进行重构与拆分，改进代码结构与可维护性。
- [Hazard804/mdcx](https://github.com/Hazard804/mdcx)：基于 sqzw-x/mdcx 继续维护与优化。
- [ZiPenOk/mdcx](https://github.com/ZiPenOk/mdcx)：基于 Hazard804/mdcx 继续开发与改进。

感谢以上项目的作者、贡献者，以及参与测试、反馈和分享经验的社区用户。

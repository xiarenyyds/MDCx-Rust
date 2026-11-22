# MDCx-Rust

基于 Rust 与 Flutter 的媒体资料刮削与管理工具，支持 Windows、macOS、Linux 桌面，以及通过浏览器使用的 Linux 服务端和 Docker 部署。

从影片识别、资料获取、目录命名，到 NFO、封面、字幕和演员资料管理，在同一套界面中完成。可通过 Telegram 接收刮削结果、切换方案并启动任务。

[下载 MDCx-Rust](https://github.com/xiarenyyds/MDCx-Rust/releases/latest) · [安装与使用](docs/INSTALLATION.md) · [功能与使用](docs/USAGE.md) · [Docker 部署](docs/DOCKER.md) · [更新日志](https://github.com/xiarenyyds/MDCx-Rust/releases) · [TG 交流群](https://t.me/mdcx_rust)

## 主要功能

| 功能 | 说明 |
| --- | --- |
| 多来源刮削 | 支持 33 个影片来源，按影片类型设置网站；速度优先取首个成功结果，字段优先按各字段排序组合资料 |
| 离线资料复用 | 按网站保存影片资料，重新刮削时按当前优先级读取本地缓存；读取与写入可独立控制 |
| 命名与资料输出 | Jinja2 模板命名、标题与简介翻译、NFO 编辑、字幕整理，以及软链接、硬链接和 STRM 输出 |
| 封面处理 | Amazon 高清封面搜索、网站图片选优、人脸裁剪、水印、手动处理及批量封面洗版 |
| 演员管理 | 对接 Emby／Jellyfin，按媒体库加载演员，查看作品，获取及同步演员资料和图片 |
| 刮削方案 | 全局共用代理、账号等设置，按方案保存刮削与洗版规则，支持导入、导出及恢复默认 |
| 通知与远程控制 | Telegram／Bark 图文通知、自定义模板，以及 Telegram 机器人任务菜单 |
| Emby 媒体助手 | 配合独立插件处理媒体库任务，配置监听、授权和媒体路径映射 |

桌面与网页使用同一套界面和 Rust 后台任务队列。切换页面或关闭网页后，已启动的后端任务继续执行；桌面软件或服务端进程需要保持运行。

## 选择版本

日常在电脑上操作可选择桌面版；在服务器或 NAS 长期运行，可选择 Docker 或 Linux CLI＋Web。CLI＋Web 提供浏览器操作界面，无需安装桌面环境。

<!-- release:downloads:start -->

| 平台 | v0.2.0 下载 |
| --- | --- |
| Windows x64 桌面 | [windows-x64.zip](https://github.com/xiarenyyds/MDCx-Rust/releases/download/v0.2.0/MDCx-Rust-v0.2.0-windows-x64.zip) |
| macOS Apple 芯片 | [macos-arm64.zip](https://github.com/xiarenyyds/MDCx-Rust/releases/download/v0.2.0/MDCx-Rust-v0.2.0-macos-arm64.zip) |
| macOS Intel | [macos-x64.zip](https://github.com/xiarenyyds/MDCx-Rust/releases/download/v0.2.0/MDCx-Rust-v0.2.0-macos-x64.zip) |
| Linux x64 桌面 | [linux-x64.tar.gz](https://github.com/xiarenyyds/MDCx-Rust/releases/download/v0.2.0/MDCx-Rust-v0.2.0-linux-x64.tar.gz) |
| Linux x64 CLI＋Web | [linux-x64-server.tar.gz](https://github.com/xiarenyyds/MDCx-Rust/releases/download/v0.2.0/MDCx-Rust-v0.2.0-linux-x64-server.tar.gz) |
| Linux ARM64 CLI＋Web | [linux-arm64-server.tar.gz](https://github.com/xiarenyyds/MDCx-Rust/releases/download/v0.2.0/MDCx-Rust-v0.2.0-linux-arm64-server.tar.gz) |
| Docker AMD64／ARM64 | [xrbzy/mdcx-rust](https://hub.docker.com/r/xrbzy/mdcx-rust)，标签 `0.2.0` 或 `latest` |

<!-- release:downloads:end -->

解压后进入与压缩包同名的文件夹，运行其中同名程序。首次启动在程序旁创建 `data`，macOS 为 `.app` 旁；移动时保留整个资料目录。环境要求及启动步骤见[安装与使用](docs/INSTALLATION.md)。

## 开始使用

1. 选择媒体目录，确认成功目录、移动、重命名和清理规则，先用少量影片检查整理结果。
2. 按影片类型选择网站与顺序，需要组合不同网站的资料时使用字段优先。
3. 在网络设置中填写需要的代理、Cookie 和接口账号，保存后开始刮削。
4. 在运行状态查看结果，在日志管理排查失败；需要时开启通知或连接媒体服务器。

数据库首次启动为空，可直接联网刮削。在数据管理页按需下载 [Amazon ASIN、演员资料和映射库](https://github.com/xiarenyyds/databases)；完整下载会替换对应本地库。影片离线资料则由实际刮削逐步积累。

## 使用文档

| 内容 | 入口 |
| --- | --- |
| 系统要求、启动与数据目录 | [安装与使用](docs/INSTALLATION.md) |
| 刮削模式、配置方案、离线资料、演员及通知 | [功能与使用](docs/USAGE.md) |
| Compose、挂载、访问令牌、媒体助手与更新 | [Docker 部署](docs/DOCKER.md) |
| Emby 插件下载及安装 | [MediaAssistant](https://github.com/xiarenyyds/MediaAssistant) |
| 可选数据库下载 | [databases](https://github.com/xiarenyyds/databases) |
| 各版本变更与历史安装包 | [Releases](https://github.com/xiarenyyds/MDCx-Rust/releases) |

网站访问受网络、账号和站点状态影响。代理和站点认证在网络设置中调整，可通过网络检测和日志管理排查连接问题。

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

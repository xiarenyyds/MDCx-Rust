# Docker 下载与使用

镜像仓库：[Docker Hub · xrbzy/mdcx-rust](https://hub.docker.com/r/xrbzy/mdcx-rust)。

支持 `linux/amd64` 和 `linux/arm64`，适用于对应架构的 Linux 服务器、NAS，以及安装了 Docker 的 Windows、macOS。Docker 自动选择设备架构。镜像已包含 CLI 后台、Web 界面和 FFprobe，无需另行下载程序压缩包或安装桌面环境。

## 下载镜像

正式版使用固定版本标签：

```sh
docker pull xrbzy/mdcx-rust:0.2.0
```

也可使用 `xrbzy/mdcx-rust:latest` 跟随最新正式版。拉取镜像只下载文件，启动后才能使用。

## 使用 Docker Compose 启动

先创建用于保存配置的目录，并准备媒体目录。将以下内容保存为 `compose.yaml`，把左侧宿主机目录和访问令牌改成自己的值：

```yaml
services:
  mdcx:
    image: xrbzy/mdcx-rust:0.2.0
    restart: unless-stopped
    ports:
      - "8080:8080"
      # 使用媒体助手时取消下一行注释。
      # - "13114:13114"
    environment:
      TZ: Asia/Shanghai
      MDCX_SERVER_TOKEN: "replace-with-your-random-token-at-least-16-characters"
    volumes:
      - /你的数据目录:/data
      - /你的媒体目录:/media
```

在该文件所在目录运行：

```sh
docker compose pull
docker compose up -d
```

浏览器打开 `http://主机地址:8080`，输入上面设置的访问令牌。令牌至少 16 个字符；可使用 `openssl rand -hex 32` 生成。关闭网页不会停止刮削，后台由容器继续运行。

登录信息保存在当前标签页，刷新后自动验证并恢复主页；令牌失效时返回登录页，临时断网保留登录信息，关闭标签页后清除。

容器默认以 UID/GID `1000:1000` 运行，配置和媒体目录都需要对该用户可读写。如需使用其他用户，在 `mdcx` 服务下增加 `user: "实际UID:实际GID"`。

## 目录与设置

| 宿主机挂载 | 容器内路径 | 用途 |
| --- | --- | --- |
| 数据目录 | `/data` | 持久保存配置、数据库、任务状态和日志 |
| 媒体目录 | `/media` | 读取媒体，并写入 NFO、封面等文件 |

网页中填写容器内路径，例如 `/media/movies`，而非宿主机原路径。首次使用选择媒体目录，配置网站、优先级、代理、Cookie 和 API 令牌，再保存设置。

镜像默认使用 `/data` 作为数据目录，无需额外设置环境变量。全局设置保存在 `/data/config/global.json`，默认刮削方案为 `/data/config/config.json`，其他方案为 `/data/config/方案名.json`；数据库、日志和缓存也位于 `/data` 下。保存或另存配置不要求配置中记录的媒体目录当前可用，实际业务操作仍检查媒体目录。

旧镜像默认使用 `/config` 作为主数据目录。将原有宿主机数据目录改为挂载到 `/data`，即可继续使用原配置及数据库，无需移动其内部文件。

多个媒体目录可分别挂载到 `/media/movies`、`/media/series`，也可直接挂载到 `/movies`、`/series` 等自定义位置。在软件中选择对应的容器内目录即可，无需额外声明媒体根目录；目录是否可读写由挂载方式和容器运行用户的文件权限决定。

容器中的 `127.0.0.1` 指向容器自身。使用宿主机代理时，应填写容器能访问的宿主机地址。

| 环境变量 | Docker 中的默认值或要求 |
| --- | --- |
| `MDCX_SERVER_TOKEN` | Web 访问令牌，建议在 Compose 中固定设置，至少 16 字符 |
| `TZ` | `Asia/Shanghai` |
| `MDCX_BIND` | `0.0.0.0:8080`，通常无需修改 |

未设置访问令牌时，后台自动生成并保存到 `/data/runtime/server-token`，可从宿主机读取。

## 连接 Emby 媒体助手

1. 在 Compose 中开放 `13114:13114` 端口，然后运行 `docker compose up -d`。
2. 在 MDCx 网页主菜单“媒体助手”中生成服务令牌，监听地址填写 `0.0.0.0`，端口填写 `13114`，保存后开始监听。
3. 在 Emby 插件中填写 `http://Docker主机地址:13114` 及同一个服务令牌。该令牌与 Web 登录令牌分别配置。
4. 路径映射的右侧填写 MDCx 容器内的媒体路径，例如 Emby 的 `/movies` 对应 MDCx 的 `/media/movies`。两个容器必须挂载同一份媒体文件。

媒体路径相同且 MDCx 可访问时无需配置映射；路径不同时，在媒体助手中添加对应的根目录映射。

插件单独从 [MediaAssistant](https://github.com/xiarenyyds/MediaAssistant) 下载，详细设置见[插件安装说明](https://github.com/xiarenyyds/MediaAssistant/blob/main/docs/INSTALLATION.md)。

## 日常管理与更新

```sh
# 查看容器状态。
docker compose ps

# 查看后台日志。
docker compose logs --tail=100 -f mdcx

# 停止或重新启动。
docker compose stop
docker compose start

# 修改部署参数后重新创建容器。
docker compose up -d
```

业务日志也可在网页“日志管理”中查看，文件保存在宿主机配置目录的 `logs/` 下，按 20 MB 分卷并保留 7 天。镜像不包含 Shell，无需进入容器操作。

更新前停止任务并备份数据目录。固定版本部署先修改 `image` 为目标版本；使用 `latest` 时无需改标签，再执行 `docker compose pull` 和 `docker compose up -d`。保留原有配置及媒体挂载，配置和数据库会继续使用。停止或移除容器不会删除宿主机挂载目录。

升级至 v0.2.0 后首次启动会自动拆分旧配置：代理、账号、通知、演员管理、工具、媒体助手和高级设置全局共用，刮削与洗版设置按方案保存。迁移前的配置备份位于 `/data/config/.backups/`。

[主程序首页](https://github.com/xiarenyyds/MDCx-Rust) · [安装与使用](https://github.com/xiarenyyds/MDCx-Rust/blob/main/docs/INSTALLATION.md) · [更新日志](https://github.com/xiarenyyds/MDCx-Rust/releases)

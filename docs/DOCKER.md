# Docker 下载与使用

镜像仓库：[Docker Hub · xrbzy/mdcx-rust](https://hub.docker.com/r/xrbzy/mdcx-rust)。

支持 `linux/amd64` 和 `linux/arm64`，适用于对应架构的 Linux 服务器、NAS，以及安装了 Docker 的 Windows、macOS。Docker 自动选择设备架构。镜像已包含 CLI 后台、Web 界面和 FFprobe，无需另行下载程序压缩包或安装桌面环境。

## 下载镜像

正式版使用固定版本标签：

```sh
docker pull xrbzy/mdcx-rust:0.1.2
```

也可使用 `xrbzy/mdcx-rust:latest` 跟随最新正式版。拉取镜像只下载文件，启动后才能使用。

## 使用 Docker Compose 启动

先创建用于保存配置的目录，并准备媒体目录。将以下内容保存为 `compose.yaml`，把左侧宿主机目录和访问令牌改成自己的值：

```yaml
services:
  mdcx:
    image: xrbzy/mdcx-rust:0.1.2
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

在该文件所在目录运行：

```sh
docker compose pull
docker compose up -d
```

浏览器打开 `http://主机地址:8080`，输入上面设置的访问令牌。令牌至少 16 个字符；可使用 `openssl rand -hex 32` 生成。关闭网页不会停止刮削，后台由容器继续运行。

登录信息保存在当前标签页，刷新后自动验证并恢复主页；令牌失效时返回登录页，临时断网保留登录信息，关闭标签页后清除。

容器默认以 UID/GID `1000:1000` 运行，配置和媒体目录都需要对该用户可读写。如需使用其他用户，在 `mdcx` 服务下增加 `user: "实际UID:实际GID"`。

## Unraid

在 Docker 模板中选择镜像 `xrbzy/mdcx-rust:0.1.2`，网络使用 bridge。Web 端口容器侧为 `8080`，宿主机可填写 `38888`；WebUI 地址填写 `http://[IP]:[PORT:8080]`。

| 模板项目 | 示例 |
| --- | --- |
| 配置路径 | `/mnt/user/appdata/mdcx-rust` → `/config`，读写 |
| 媒体路径 | `/mnt/user/media` → `/media`，读写 |
| 访问令牌变量名 | `MDCX_SERVER_TOKEN`，名称前后不要有空格 |
| 额外参数 | `--user 99:100`，适用于使用 nobody:users 的共享目录 |

镜像默认用户为 `1000:1000`，不会自动修改宿主机目录所有者。Unraid 共享目录若归 `nobody:users`（通常为 `99:100`）所有，可通过上面的额外参数使用相同身份；自定义目录权限时应填写实际 UID/GID。`PUID`、`PGID` 环境变量不会改变本镜像的运行用户。

若日志显示“后台启动失败：Permission denied (os error 13)”，先停止容器，再在 Unraid 终端修正**配置目录**的所有者和所有者读写权限。以下路径按实际配置修改：

```sh
mkdir -p /mnt/user/appdata/mdcx-rust
chown -R 99:100 /mnt/user/appdata/mdcx-rust
chmod -R u+rwX /mnt/user/appdata/mdcx-rust
```

在容器模板“高级视图 → 额外参数”填写 `--user 99:100`，应用并重新创建容器。配置路径、已有子目录及媒体目录都必须允许该用户访问；写入 NFO 和图片还需媒体目录的写权限。修正配置目录不需要递归修改整个媒体库。

若媒体助手也需对接 Emby，再添加容器端口 `13114`。Web 登录使用 `MDCX_SERVER_TOKEN`，媒体助手令牌在网页中另行生成。

## 目录与设置

| 宿主机挂载 | 容器内路径 | 用途 |
| --- | --- | --- |
| 配置目录 | `/config` | 持久保存配置、数据库、任务状态和日志 |
| 媒体目录 | `/media` | 读取媒体，并写入 NFO、封面等文件 |

网页中填写容器内路径，例如 `/media/movies`，而非宿主机原路径。首次使用选择媒体目录，配置网站、优先级、代理、Cookie 和 API 令牌，再保存设置。

多个媒体目录可分别挂载到 `/media/movies`、`/media/series` 等子目录。若挂载到 `/movies`、`/series` 等其他位置，还需在 `environment` 中设置 `MDCX_MEDIA_ROOTS: "/movies;/series"`，多个根目录用分号分隔。

容器中的 `127.0.0.1` 指向容器自身。使用宿主机代理时，应填写容器能访问的宿主机地址。

| 环境变量 | Docker 中的默认值或要求 |
| --- | --- |
| `MDCX_SERVER_TOKEN` | Web 访问令牌，建议在 Compose 中固定设置，至少 16 字符 |
| `MDCX_MEDIA_ROOTS` | `/media`，多个可操作媒体根目录用分号分隔 |
| `TZ` | `Asia/Shanghai` |
| `MDCX_BIND` | `0.0.0.0:8080`，通常无需修改 |
| `MDCX_RUST_DATA_DIR` | `/config`，与配置挂载目录保持一致 |

未设置访问令牌时，后台自动生成并保存到配置目录的 `runtime/server-token` 文件中，可从宿主机读取。

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

更新前停止任务并备份配置目录。固定版本部署先修改 `image` 为目标版本；使用 `latest` 时无需改标签，再执行 `docker compose pull` 和 `docker compose up -d`。保留原有配置及媒体挂载，配置和数据库会继续使用。停止或移除容器不会删除宿主机挂载目录。

[主程序首页](https://github.com/xiarenyyds/MDCx-Rust) · [安装与使用](https://github.com/xiarenyyds/MDCx-Rust/blob/main/docs/INSTALLATION.md) · [更新日志](../CHANGELOG.md)

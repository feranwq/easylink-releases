# EasyLink

EasyLink 是用于个人设备之间同步剪贴板、文件和通知的工具。本仓库只发布客户端二进制和部署说明，不包含应用源码。

## 下载客户端

- [macOS Apple Silicon（M 系列）](https://github.com/feranwq/easylink-releases/releases/latest/download/easylink_macos_arm64.dmg)
- [macOS Intel](https://github.com/feranwq/easylink-releases/releases/latest/download/easylink_macos_x64.dmg)
- [所有版本与实际已发布的平台文件](https://github.com/feranwq/easylink-releases/releases)

打开 DMG 后将 `EasyLink.app` 拖入“应用程序”。客户端使用自己部署的服务器地址及 API Key，不提供默认公共服务器。Windows 安装包由 Windows 构建后发布，以 Release 中实际列出的文件为准。

客户端更新检查使用本仓库的 GitHub 最新稳定 Release，下载对应架构的安装包。macOS 下载的是 DMG；未集成 Sparkle 的安装包仍需按提示完成安装，不会静默替换应用。GitHub 无法访问时更新检查会失败，但不会影响已配置的自建服务器同步。

## Docker Compose 启动服务端

镜像：[`wqferan/easylink-server`](https://hub.docker.com/r/wqferan/easylink-server)，支持 `linux/amd64` 和 `linux/arm64`。个人单实例部署可以直接使用内置 SQLite，无需 OpenSaaS、PostgreSQL、MinIO 或外部 Docker 网络。

在新目录中创建 `.env`，填写管理员登录信息：

```dotenv
EASYLINK_ADMIN_USERNAME=admin
EASYLINK_ADMIN_PASSWORD=replace-with-a-long-random-password
```

创建 `compose.yaml`：

```yaml
services:
  easylink-server:
    image: wqferan/easylink-server:latest
    restart: unless-stopped
    ports:
      - "127.0.0.1:5033:5033"
    environment:
      SYNCCLIPBOARD_USERNAME: ${EASYLINK_ADMIN_USERNAME:-admin}
      SYNCCLIPBOARD_PASSWORD: ${EASYLINK_ADMIN_PASSWORD:?Set EASYLINK_ADMIN_PASSWORD in .env}
      AppSettings__ApiKeyAuth__Enabled: "true"
      AppSettings__ApiKeyAuth__ValidationUrl: ""
      AppSettings__Database__Provider: sqlite
      AppSettings__NativeNotification__Enabled: "true"
      AppSettings__NativeNotification__Desktop__Enabled: "true"
      AppSettings__PlanEnforcement__Enabled: "false"
      Logging__LogLevel__Default: Information
      Logging__LogLevel__Microsoft.EntityFrameworkCore.Database.Command: Warning
    volumes:
      - ./server-data:/app/data
```

启动：

```bash
chmod 600 .env
docker compose pull
docker compose up -d
docker compose logs --tail=100 easylink-server
```

访问服务器本机的 `http://127.0.0.1:5033/admin/api-keys`，用 `.env` 中的管理员用户名/密码登录并创建 API Key。API Key 只会在创建时完整显示，妥善保存后填入各客户端。

- Basic 用户名/密码只用于管理页面，不能用于剪贴板、通知或 SignalR API。
- 客户端 API 使用 `Authorization: Bearer <apiKey>` 或 `X-API-Key: <apiKey>`。
- 同一用户的所有 Key 共用剪贴板、通知和历史数据，不再使用通知频道隔离。
- 用量仍会统计；上述配置不启用套餐拦截。
- APNs/FCM 默认未启用，iOS 远程推送需要另外配置自己的 APNs 凭证，不能仅靠启用桌面通知获得 iOS 推送。

### HTTPS 与反向代理

示例只把端口绑定在服务器本机，推荐通过已有反向代理提供 HTTPS。配置上游为 `http://127.0.0.1:5033` 并转发 WebSocket（SignalR 的 `/SyncClipboardHub`）。如果代理在另一容器，应把两者加入同一 Docker 网络并使用 `http://easylink-server:5033`，而不是容器内的 `127.0.0.1`。

临时局域网测试可以改为 `5033:5033`，但不要直接通过公网明文 HTTP 传输管理员密码、API Key 或个人数据。

### 数据、备份与升级

数据库、API Key 哈希、文件和历史保存在 `./server-data`，删除该目录会丢失数据。启动时服务端自动执行数据库迁移，不需要手动建表。

升级前停止服务并备份整个 `server-data`，然后执行：

```bash
docker compose pull
docker compose up -d
docker compose logs --tail=100 easylink-server
```

**升级警告：本次移除通知频道的数据库迁移会清空旧通知设备、通知历史、投递及回执记录；客户端需要重新注册设备。此通知迁移不清空剪贴板数据，但更早版本到当前版本可能包含其他破坏性迁移。请完整备份，不能仅通过回退镜像恢复已删除的数据。**

SQLite 适合个人单实例；需要多实例或更大规模时，使用独立 PostgreSQL 并配置 `AppSettings__Database__Provider=postgres` 和 `AppSettings__Database__ConnectionString`。不要让多个容器共享同一 SQLite 文件。

## 授权与致谢

EasyLink 基于开源项目 [SyncClipboard](https://github.com/Jeric-X/SyncClipboard) 二次开发。客户端中可查看开源依赖与授权说明；发布文件的版本及平台以各 Release 描述为准。

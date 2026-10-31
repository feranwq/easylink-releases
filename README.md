# EasyLink

EasyLink 是用于个人设备之间同步剪贴板、文件和通知的工具。本仓库只发布客户端二进制和部署说明，不包含应用源码。

隐私政策： [Android APK 中文](docs/android-privacy-policy.md) · [Android APK English](docs/en-us/android-privacy-policy.md) · [Google Play 中文](docs/android-play-privacy-policy.md) · [Google Play English](docs/en-us/android-play-privacy-policy.md) · [iOS 中文](docs/privacy-policy.md) · [iOS English](docs/en-us/privacy-policy.md)

## 下载客户端

- [macOS Apple Silicon（M 系列）](https://github.com/feranwq/easylink-releases/releases/latest/download/easylink_macos_arm64.dmg)
- [macOS Intel](https://github.com/feranwq/easylink-releases/releases/latest/download/easylink_macos_x64.dmg)
- [所有版本与实际已发布的平台文件](https://github.com/feranwq/easylink-releases/releases)

打开 DMG 后将 `EasyLink.app` 拖入“应用程序”。客户端使用自己部署的服务器地址及 API Key，不提供默认公共服务器。Windows 安装包由 Windows 构建后发布，以 Release 中实际列出的文件为准。

客户端更新检查使用本仓库的 GitHub 最新稳定 Release，下载对应架构的安装包。macOS 下载的是 DMG；未集成 Sparkle 的安装包仍需按提示完成安装，不会静默替换应用。GitHub 无法访问时更新检查会失败，但不会影响已配置的自建服务器同步。

## Docker Compose 启动服务端

本仓库的 `compose.yaml` 部署 EasyLink Server、MinIO、Nginx 和 Certbot。服务端使用 SQLite；文件对象保存在 MinIO，上传/下载通过 S3 预签名链接直传。

该配置为公网 IP 申请 Let's Encrypt HTTPS 证书。IP 证书有效期约 160 小时（约 6 天），Certbot 每 12 小时自动检查续期。需要固定公网 IPv4/IPv6，并确保防火墙放行 TCP `80`、`443`、`9000`。80 用于 ACME HTTP-01 验证，443 提供 EasyLink API，9000 提供 TLS S3 API。MinIO 管理端口 9001 不对公网开放。

从本仓库创建部署目录：

```bash
git clone --depth 1 https://github.com/feranwq/easylink-releases.git easylink-server
cd easylink-server
cp .env.example .env
```

编辑 `.env`：填写本机公网 IP、可接收证书通知的邮箱，并将 EasyLink 和 MinIO 密码改成长随机值。首次申请证书：

```bash
chmod 600 .env
mkdir -p server-data miniodata certbot/www letsencrypt
docker compose --profile certificate-setup up -d easylink-server minio nginx-acme-bootstrap
docker compose --profile certificate-setup run --rm certbot-issue
docker compose --profile certificate-setup stop nginx-acme-bootstrap
docker compose up -d nginx certbot-renew
docker compose ps
docker compose logs --tail=100 easylink-server nginx certbot-renew
```

之后升级镜像：

```bash
docker compose pull
docker compose up -d
docker compose logs --tail=100 easylink-server
```

管理页面为 `https://<公网IP>/admin/api-keys`；仅本机访问也可用 `http://127.0.0.1:5033/admin/api-keys`。Basic 用户名/密码只用于管理页面，客户端 API 必须使用创建时显示的 API Key。MinIO 的 S3 API 由 `https://<公网IP>:9000` 提供，管理端口 9001 不应映射到公网。

- EasyLink 数据库及本地历史文件位于 `./server-data`，MinIO 对象位于 `./miniodata`，证书和续期状态位于 `./letsencrypt`；升级或迁移前应停止服务并备份这些目录。
- `.env.example` 默认保留当前部署使用的 Bitnami legacy MinIO 镜像以避免隐式换代；该镜像已归档且不再维护。新部署请在确认兼容性后通过 `MINIO_IMAGE` 选择维护中的 S3 兼容镜像。
- 公网 HTTPS 证书绑定当前 IP；IP 变化后需更新 `.env` 并重新申请证书。
- 首次从旧版本升级前请完整备份。当前版本的通知频道移除迁移会清理旧通知设备、历史、投递与回执；较早版本也可能包含其他破坏性迁移。
- APNs/FCM 默认未启用。iOS 远程推送需要另行配置自己的 APNs 凭证。
- 如服务器已有 Nginx 占用 80/443，需先调整现有入口或改用服务端单容器 Compose 配置，不能同时绑定同一端口。

## 授权与致谢

EasyLink 基于开源项目 [SyncClipboard](https://github.com/Jeric-X/SyncClipboard) 二次开发。客户端中可查看开源依赖与授权说明；发布文件的版本及平台以各 Release 描述为准。

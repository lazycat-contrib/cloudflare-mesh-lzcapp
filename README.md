# Cloudflare Mesh for LazyCat

将 `cloudflare/mesh` 转换为 LazyCat LPK v2 应用。Mesh 节点在 Linux 服务器上以无头模式运行 Cloudflare One Client（`warp-cli`）。安装时通过设置向导输入 Mesh Node Token，节点服务随后保持后台运行。

- 上游镜像：`cloudflare/mesh:2026.7.0`
- 软件包：`community.lazycat.app.cloudflare-mesh`
- 官方文档：[Cloudflare Mesh](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-mesh/)
- 数据目录：`/lzcapp/var/mesh`

## 本地构建

```bash
lzc-cli project release -o dist/application.lpk
lzc-cli lpk info dist/application.lpk
```

## GitHub Actions

`.github/workflows/publish.yml` 每天检查符合 `YYYY.M.PATCH` 格式的稳定镜像标签，也支持手动触发。发现新版本后会把上游镜像复制到 LazyCat Registry、更新包版本、创建带版本号的 GitHub Release 资产，并分别发布到 LazyCat 官方商店与喵喵私有商店。

必需的 GitHub Actions Secrets：

- `APPSTORE_URL`
- `APPSTORE_TOKEN`
- `LZC_API_TOKEN`

可选 Secrets：

- `APP_ID`
- `PRIVATE_STORE_GROUP_CODES`
- `LZC_API_HOST`

如果使用 Organization Secrets，需要显式授权本仓库。相同名称同时存在时，Environment Secret 优先于 Repository Secret，Repository Secret 优先于 Organization Secret。

## 兼容性说明

Cloudflare Mesh 需要 `NET_ADMIN`、`NET_RAW`、`/dev/net/tun` 与 IP 转发 sysctl。`NET_ADMIN` 使用 LazyCat 原生 `netadmin` 字段，其余能力通过 `compose_override` 提供。`compose_override` 不承诺兼容性；上传喵喵商店前，请在 LazyCat 开发者群确认目标系统允许 TUN 设备直通和这些容器能力。

项目保留原始 Compose 的健康检查行为：上游未提供 healthcheck，因此没有自行添加可能导致误判的探针。

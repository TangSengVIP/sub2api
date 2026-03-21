# Sub2API 魔改版部署

> TangSengVIP 魔改版，基于 Wei-Shaw/sub2api dev 分支定制。
>
> 魔改内容：深色赛博朋克主题、主题色 #06b6d4 (Cyan)、强制深色模式、删除 SaaS 相关功能。

---

## 快速开始

### Docker 一键部署（推荐）

```bash
# 创建部署目录
mkdir -p sub2api-deploy && cd sub2api-deploy

# 下载部署准备脚本
curl -sSL https://raw.githubusercontent.com/TangSengVIP/sub2api/dev/deploy/docker-deploy.sh -o docker-deploy.sh
chmod +x docker-deploy.sh

# 运行准备脚本（自动生成密钥、创建目录）
./docker-deploy.sh

# 启动服务
docker compose up -d
```

**脚本自动完成：**
- 下载 `docker-compose.local.yml` 和 `.env.example`
- 生成安全密钥（JWT_SECRET、TOTP_ENCRYPTION_KEY、POSTGRES_PASSWORD）
- 创建 `.env` 文件
- 创建数据目录（data/、postgres_data/、redis_data/）

### 访问

- **Web 管理后台**: http://你的服务器IP:8080
- **管理员账号**: `admin@sub2api.local`
- **管理员密码**: 自动生成，查看日志：

```bash
docker compose logs sub2api | grep "admin password"
```

### 快速验证

```bash
curl http://127.0.0.1:8080/health
```

---

## Docker 部署版本对比

| 版本 | 数据存储 | 迁移便利性 | 适用场景 |
|------|---------|-----------|---------|
| **docker-compose.local.yml**（本项目使用） | 本地目录 | 简单（打包整个目录） | 生产环境、频繁备份 |
| **docker-compose.yml** | Docker 命名卷 | 需要 docker 命令 | 简单设置 |

---

## 自定义镜像构建（重要）

TangSengVIP 没有发布独立 Docker 镜像，**必须从源码构建**：

### 构建镜像

```bash
# 克隆源码
git clone https://github.com/TangSengVIP/sub2api.git
cd sub2api

# 构建镜像（需要 3-5 分钟）
docker build -t sub2api:tangseng -f Dockerfile .
```

### 使用自定义镜像

```bash
# 编辑 docker-compose.yml，找到 image 行
# 将  image: weishaw/sub2api:latest
# 改为 image: sub2api:tangseng

docker compose up -d
```

### 升级魔改版

```bash
cd /path/to/sub2api

# 拉取最新代码
git checkout dev && git pull

# 重新构建镜像
docker build -t sub2api:tangseng -f Dockerfile .

# 重启服务
cd /path/to/sub2api-deploy
docker compose up -d --force-recreate sub2api
```

---

## 手动部署

### 1. 配置环境变量

```bash
cd deploy
cp .env.example .env
nano .env
```

**必须配置项：**

```bash
# PostgreSQL 密码
POSTGRES_PASSWORD=your_secure_password

# JWT 密钥（固定不变，重启后保持登录态）
JWT_SECRET=$(openssl rand -hex 32)

# TOTP 加密密钥（固定不变，重启后保留 2FA）
TOTP_ENCRYPTION_KEY=$(openssl rand -hex 32)
```

### 2. 创建数据目录

```bash
mkdir -p data postgres_data redis_data
```

### 3. 启动服务

```bash
docker compose -f docker-compose.local.yml up -d

# 查看日志
docker compose -f docker-compose.local.yml logs -f sub2api
```

---

## 常用命令

```bash
# 查看状态
docker compose -f docker-compose.local.yml ps

# 查看日志
docker compose -f docker-compose.local.yml logs -f sub2api

# 重启服务
docker compose -f docker-compose.local.yml restart sub2api

# 停止服务
docker compose -f docker-compose.local.yml down

# 完全删除（慎用，会清除数据）
docker compose -f docker-compose.local.yml down -v
rm -rf data/ postgres_data/ redis_data/
```

---

## 反向代理配置

### Nginx（推荐）

如果通过 Nginx 反向代理，**必须在 `http` 块中添加**：

```nginx
underscores_in_headers on;
```

Nginx 默认丢弃含下划线的请求头（如 `session_id`），会导致多账号粘性会话失效。

**`server.trusted_proxies` 配置：**

如果 Sub2API 前面有 Nginx/Docker 代理，需要在 `config.yaml` 中配置信任的代理 IP：

```yaml
server:
  trusted_proxies:
    - "127.0.0.1/32"
    - "172.17.0.0/16"
    - "10.0.0.0/8"
```

挂载方式：将 `config.yaml` 挂载到容器内 `/app/data/config.yaml`，重启即可。

### Claude Code 配置示例

```bash
export ANTHROPIC_BASE_URL="https://api.titslee.net/antigravity"
export ANTHROPIC_AUTH_TOKEN="sk-你的API密钥"
```

### OpenAI 兼容配置

```bash
export OPENAI_BASE_URL="https://api.titslee.net/v1"
export OPENAI_API_KEY="sk-你的API密钥"
```

---

## 环境变量说明

| 变量 | 必需 | 默认值 | 说明 |
|------|------|--------|------|
| `POSTGRES_PASSWORD` | **是** | - | PostgreSQL 密码 |
| `JWT_SECRET` | **推荐** | 自动生成 | JWT 会话密钥 |
| `TOTP_ENCRYPTION_KEY` | **推荐** | 自动生成 | 双因素认证加密密钥 |
| `SERVER_PORT` | 否 | `8080` | 服务端口 |
| `ADMIN_EMAIL` | 否 | `admin@sub2api.local` | 管理员邮箱 |
| `ADMIN_PASSWORD` | 否 | 自动生成 | 管理员密码 |
| `TZ` | 否 | `Asia/Shanghai` | 时区 |
| `SECURITY_URL_ALLOWLIST_ENABLED` | 否 | `false` | URL 白名单检查 |
| `SECURITY_URL_ALLOWLIST_ALLOW_INSECURE_HTTP` | 否 | `false` | 允许 HTTP URL |

---

## API 端点

| 端点 | 说明 |
|------|------|
| `/v1/*` | OpenAI 兼容 API |
| `/antigravity/v1/*` | Antigravity Claude |
| `/antigravity/v1beta/*` | Antigravity Gemini |
| `/api/v1/*` | 管理后台 API |

---

## 数据迁移

使用 `docker-compose.local.yml` 便于整体迁移：

```bash
# 打包（源服务器）
docker compose -f docker-compose.local.yml down
cd ..
tar czf sub2api-backup.tar.gz deploy/

# 传输到新服务器
scp sub2api-backup.tar.gz user@new-server:/opt/

# 新服务器解压
tar xzf sub2api-backup.tar.gz

# 重新构建镜像
cd /path/to/sub2api
git pull && docker build -t sub2api:tangseng -f Dockerfile .

# 启动
cd deploy
docker compose -f docker-compose.local.yml up -d
```

---

## 故障排查

### 容器启动失败

```bash
docker compose -f docker-compose.local.yml logs sub2api
```

### 健康检查不通过

PostgreSQL 首次初始化需要时间，等待 30 秒后重试：

```bash
docker compose -f docker-compose.local.yml ps
docker compose -f docker-compose.local.yml restart sub2api
```

### 健康检查正常但页面 502

检查 Nginx 是否正确代理：

```bash
curl http://127.0.0.1:8080/health
curl https://你的域名/health
```

### 粘性会话不生效（Claude Code 飘号）

确保 Nginx 配置了 `underscores_in_headers on`，参见上方 Nginx 配置说明。

### 忘记管理员密码

```bash
# 停止服务
docker compose -f docker-compose.local.yml down

# 编辑 .env 设置新密码
ADMIN_PASSWORD=MyNewPassword123

# 启动服务
docker compose -f docker-compose.local.yml up -d
```

---

## 相关链接

- **魔改版源码**: https://github.com/TangSengVIP/sub2api (dev 分支)
- **上游仓库**: https://github.com/Wei-Shaw/sub2api
- **一键安装脚本**: https://raw.githubusercontent.com/TangSengVIP/sub2api/dev/deploy/install.sh

---

## 免责声明

> **使用本项目前请仔细阅读：**
>
> **服务条款风险**: 使用本项目可能违反 Anthropic 的服务条款。请在使用前仔细阅读 Anthropic 的用户协议，使用本项目的一切风险由用户自行承担。
>
> **免责声明**: 本项目仅供技术学习和研究使用，作者不对因使用本项目导致的账户封禁、服务中断或其他损失承担任何责任。

# 部署指南

本项目推荐使用 Docker Compose 部署。运行数据保存在宿主机的 `data/` 目录，容器重新创建后不会丢失。

## 准备环境

准备一台能够访问 NodeSeek RSS 和 Telegram API 的 Linux 服务器，并安装 Git、Docker 与 Docker Compose v2。

在 Debian 或 Ubuntu 上可以执行：

```bash
sudo apt update
sudo apt install -y git docker.io docker-compose-plugin
sudo systemctl enable --now docker
```

## 下载与配置

```bash
git clone https://github.com/suckdrygod/nodeseek-rss-telegram-bot.git
cd nodeseek-rss-telegram-bot
cp .env.example .env
```

编辑 `.env`，至少填写从 Telegram `@BotFather` 获取的 `BOT_TOKEN`：

```text
BOT_TOKEN=你的机器人令牌
```

可选配置：

- `ALLOWED_USER_IDS`：允许使用机器人的 Telegram 用户 ID，多个 ID 用英文逗号分隔；留空表示不启用白名单。
- `POLL_INTERVAL_SECONDS`：RSS 轮询间隔。
- `LOG_LEVEL`：日志级别，默认使用 `INFO`。

其他选项可直接参考 `.env.example`。不要把真实 `.env` 提交到 GitHub。

## 启动

```bash
docker compose up -d --build
docker compose ps
```

容器名称应为 `nodeseek-rss-bot`，状态应显示为 `Up`。查看日志：

```bash
docker compose logs --tail=100 -f
```

日志出现 `Application started` 表示启动完成。按 `Ctrl+C` 只会退出日志查看，不会停止服务。

## 更新

```bash
git pull
docker compose up -d --build
```

更新前如需备份数据，可复制整个 `data/` 目录。项目使用 `restart: unless-stopped`，服务器重启后容器会自动恢复运行。

## 停止或卸载

停止容器但保留数据：

```bash
docker compose down
```

需要彻底删除时，再手动处理项目目录和 `data/`。不要误删仍需保留的 SQLite 数据库。

## 安全说明

- 不要公开 `.env`、`BOT_TOKEN` 或 `data/`。
- 如果令牌泄露，请立即通过 `@BotFather` 重置。
- 建议启用 `ALLOWED_USER_IDS`，限制可以管理订阅和推送目标的用户。

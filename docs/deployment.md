# 部署说明

## 线上架构

```text
Nginx
  ↓ Unix Socket
Gunicorn
  ↓
Django
```

当前配置：

```text
workers: 1
worker class: gthread
threads: 8
model concurrency: 4
```

单 worker 是为了让进程内的轮次状态和闸门一致。  
多 worker 需要把状态迁移到 Redis 或数据库。

## 前端构建

```bash
cd frontend
npm install
npm run build
```

产物输出到：

```text
backend/static/frontend/
```

## 后端启动

```bash
cd backend
gunicorn \
  --workers 1 \
  --worker-class gthread \
  --threads 8 \
  --graceful-timeout 30 \
  --timeout 300 \
  --bind unix:/path/to/gunicorn.sock \
  backend.wsgi:application
```

## 环境变量

```env
API_BASE=
API_KEY=

EVALUATION_MODEL=qwen3.8-flash
GROUP_CHAT_MODEL_CONCURRENCY=4

WEATHER_API_KEY=
WEATHER_API_HOST=

DJANGO_SECRET_KEY=
DJANGO_DEBUG=False
DJANGO_ALLOWED_HOSTS=
DJANGO_CSRF_TRUSTED_ORIGINS=
```

## 静态资源

```text
Nginx 提供 /static/
Django 提供 /media/
```

## 验证

```bash
curl -I https://your-domain/
curl -I https://your-domain/static/frontend/index.html
```

服务器日志：

```bash
tail -f ~/gunicorn.log
```

重点看：

```text
批量评估成功
批量评估失败
群聊生成模型
单聊生成模型
429
```

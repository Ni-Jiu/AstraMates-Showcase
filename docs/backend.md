# 后端设计

## 技术栈

- Django 6
- Django REST Framework
- SimpleJWT
- LangChain / LangGraph
- SQLite（当前）
- LanceDB
- Gunicorn

## 模块

```text
views/
├── assistant/   总管 AI、知识库、工具调用
├── create/      AI 角色创建与编辑
├── friend/      单聊、好友、聊天记录
├── groupchat/   群聊、成员、消息、调度
├── homepage/    首页数据
└── user/        登录、注册、鉴权、资料
```

## 鉴权

使用 JWT：

```text
access token
refresh token
```

前端在请求头中携带：

```text
Authorization: Bearer <access_token>
```

## 群聊接口

```text
POST /api/group-chat/message/chat/
GET  /api/group-chat/message/history/
POST /api/group-chat/message/stop/
GET  /api/group-chat/generation-models/
POST /api/group-chat/message/model/
```

群聊使用 SSE 流式返回。

## 单聊接口

```text
POST /api/friend/message/chat/
POST /api/friend/message/stop/
GET  /api/friend/message/get_history/
GET  /api/friend/message/export/
```

## 文档解析

支持：

```text
PDF
DOCX
TXT
MD
CSV
XLSX
```

上传后解析文本，截断后进入上下文。

## 模型配置

```text
API_BASE
API_KEY
EVALUATION_MODEL
GROUP_CHAT_MODEL_CONCURRENCY
```

评估模型固定，生成模型可由用户选择。

## 并发闸门

`FairModelGate` 使用信号量限制同时执行的模型请求数量：

```text
GROUP_CHAT_MODEL_CONCURRENCY=4
```

当前部署为：

```text
1 worker
8 threads
4 个模型并发
```

## 失败处理

- 429：全局冷却 + 重试
- 5xx：确认过载后重试
- 普通 500：不重试
- 单个角色失败：跳过
- 连续两个角色失败：结束本轮

# 系统架构

## 总览

AstraMates 采用前后端分离架构：

```text
浏览器
  ↓ HTTP / SSE
Vue 3 前端
  ↓ REST API
Django + Django REST Framework
  ├── 用户与鉴权
  ├── AI 角色
  ├── 单聊
  ├── 群聊
  ├── 总管 AI
  ├── 文档解析
  └── 多智能体调度
        ├── 评估模型
        ├── 生成模型
        └── 本地兜底
```

## 后端分层

```text
backend/
├── backend/            Django 配置、URL、WSGI
└── web/
    ├── models/         数据模型
    ├── views/          业务接口
    ├── documents/      文档解析、向量库
    ├── migrations/     数据库迁移
    └── templates/      页面模板
```

`views/` 按业务拆分：

```text
views/
├── assistant/   总管 AI
├── create/      AI 角色创建
├── friend/      单聊
├── groupchat/   群聊
├── homepage/    首页
└── user/        登录、注册、资料
```

## 群聊链路

```text
POST /api/group-chat/message/chat/
  ↓
解析消息、图片、文档
  ↓
解析 @ / 自然点名 / 硬指令
  ↓
构造 GroupChatSchedulerV2
  ↓
plan_round()
  ↓
本地粗筛 → 评估模型 → 综合分 → 发言顺序
  ↓
SSE 流式生成
  ↓
写入消息、更新预览、记录发言次数
```

## 模型层

模型服务通过 OpenAI 兼容接口接入：

```text
API_BASE + API_KEY
  ├── 评估模型：Qwen3.8-Flash
  └── 生成模型：用户选择
```

评估和生成互不影响：

- 评估固定
- 生成可选
- 模型选择不参与调度评分

## 流式通信

群聊和单聊都使用 SSE：

```text
agent_start
agent_chunk
agent_complete
agent_error
[DONE]
```

前端根据事件增量更新消息气泡。

## 数据与存储

当前单机部署使用 SQLite，存储：

- 用户与角色
- 好友关系
- 单聊消息
- 群聊、成员、消息
- 文档与图片元数据
- 预览与轮次状态

知识库使用 LanceDB 做向量检索。

## 稳定性

```text
模型闸门：限制并发请求
429：冷却 + 重试
5xx：确认过载后重试
评估失败：缓存 / 本地兜底
生成失败：跳过角色，不终止整轮
```

# 后端模块

后端基于 Django + Django REST Framework。

## 业务划分

```text
assistant/   总管 AI、知识库、工具调用
create/      AI 角色创建与编辑
friend/      单聊、好友、聊天记录
groupchat/   群聊、成员、消息、调度
homepage/    首页数据
user/        登录、注册、鉴权、资料
```

## 关键能力

- JWT 鉴权
- SSE 流式返回
- 文档解析
- 图片上传
- 聊天记录导出
- 模型闸门与失败隔离

详细说明：

- [后端设计](../../docs/backend.md)
- [系统架构](../../docs/architecture.md)
- [调度系统](../../docs/scheduler.md)

# 前端设计

## 技术栈

- Vue 3
- Vite
- Pinia
- Vue Router
- Axios
- SSE

## 页面

```text
views/
├── assistant/   总管 AI
├── create/      角色创建
├── friend/      单聊
├── groupchat/   群聊
├── homepage/    首页
└── user/        登录、注册
```

## 群聊页面

主要组成：

```text
GroupChatIndex
├── 群聊列表
├── 消息区
├── GroupChatInput
│   ├── 文本输入
│   ├── 图片 / 文档 / 语音
│   └── 模型选择
└── GroupChatSettingsPanel
```

## 单聊页面

```text
ChatField
├── ChatHistory
├── InputField
└── 更多设置
    ├── 导出聊天
    └── 回复模型
```

单聊的模型选择按“用户 + 角色”保存，不影响其他用户。

## SSE

前端使用 `streamApi` 处理：

```text
user_message
agent_start
agent_chunk
agent_complete
agent_error
[DONE]
```

收到 `agent_chunk` 时增量更新对应消息。

## 模型选择

群聊：

```text
输入框右下角
```

单聊：

```text
右上角 ··· → 当前模型 → 选择
```

切换规则：

- 当前正在生成的回复不打断
- 下一个尚未开始生成的角色使用新模型

## 状态管理

Pinia 管理：

- 用户信息
- 音乐播放
- 页面共享状态

聊天草稿、当前轮次、模型偏好使用浏览器本地存储。

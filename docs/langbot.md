# LangBot 接入 ShannonAPI

LangBot 采用「供应商（Provider）+ 模型（Model）」两层架构，供应商定义 Base URL 和 Key，模型继承供应商配置。

## v3：provider.json

编辑 `data/provider.json`：

```json
{
  "keys": {
    "openai": ["sk-你的Key"]
  },
  "requester": {
    "openai-chat-completions": {
      "base-url": "https://shannonapi.xyz/v1",
      "args": {},
      "timeout": 120
    }
  },
  "model": "claude-sonnet-5-5"
}
```

要点：

- `base-url` **必须带 `/v1`。
- `timeout` 默认 120 秒；长文本或长流式输出建议调到 300。
- 模型元数据在 `data/metadata/llm-models.json`，列表里没有的模型 ID 需要手动补一条。

## v4+：WebUI 两层架构

1. **模型** → 新建供应商：请求器（requester）选 `openai-chat-completions`，Base URL 填 `https://shannonapi.xyz/v1`，API Key 填控制台 Key
2. 在供应商下添加模型：名称填模型 ID，如 `claude-sonnet-5-5`
3. 需要 function calling / MCP 工具时，勾选模型的 **工具使用能力**（模型本身需支持 function calling）
4. 在流水线里把该模型设为默认对话模型

## 实用技巧

- 群聊机器人建议配多个 Key 轮换，避免限流时整个机器人无响应。
- 长上下文场景用 Claude Kiro 组的 `claude-sonnet-5-5`（1M 上下文），群聊记录很长的场景优势明显。

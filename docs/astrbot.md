# AstrBot 接入 ShannonAPI

AstrBot 支持 OpenAI、Anthropic、Google GenAI 三种原生 API 格式，ShannonAPI 的两种协议都可以直接接。

## WebUI 配置（推荐）

1. 打开 AstrBot 管理面板 → **模型提供商** → **对话** → **新增**
2. 服务类型选 **OpenAI Compatible**：
   - 提供商名称：`ShannonAPI`
   - API Base URL：`https://shannonapi.xyz/v1`（**必须带 `/v1`**）
   - API Key：控制台创建的 Key
3. 点 **保存并获取模型**，在模型列表里给 `claude-sonnet-5-5`、`deepseek-v4-flash` 等点右侧 **+** 启用
4. 拉取不到模型列表时：先 **保存配置**，再 **自定义模型** 手填模型 ID
5. 点模型旁的 **测试模型** 按钮验证连通性
6. 进入 **配置文件** → **AI 配置** → **模型**，把「对话模型」设为刚添加的模型，保存配置

## Anthropic 原生格式

新增时类型选 **Anthropic**，API Base 填 `https://shannonapi.xyz`（**不带 `/v1`**，AstrBot 自动拼 `/v1/messages`）。Claude 模型用原生格式时 Tool Call 兼容性更好。

## 配置文件方式（data/cmd_config.json）

提供商连接保存在 `provider_sources` 字段，具体模型配置保存在 `provider` 字段：

```json
{
  "provider_sources": [
    {
      "id": "shannonapi",
      "type": "openai_chat_completion",
      "api_base": "https://shannonapi.xyz/v1",
      "key": ["sk-你的Key"],
      "enable": true
    }
  ]
}
```

（字段结构随版本可能有调整，WebUI 改完后可在 `cmd_config.json` 里核对。）

## 实用技巧

- **多 Key 负载均衡**：`key` 数组填多个 Key，某个 Key 触发限流时自动切换，群聊场景很实用。
- **模型混搭**：默认对话用 `claude-sonnet-5-5`，给「画图」「总结」等独立任务配 `deepseek-v4-flash` 之类的低价模型，成本差距很大。
- **环境变量**：API Key 字段可填 `$SHANNON_API_KEY` 形式从环境变量读取，避免明文写配置。

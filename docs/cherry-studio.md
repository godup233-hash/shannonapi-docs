# cherry-studio 接入 ShannonAPI

cherry-studio 是桌面端多模型客户端，支持 Anthropic 与 OpenAI 两种提供商类型。

## 方式一：Anthropic 类型（推荐）

1. 左下角 **设置** → **模型服务** → **添加**
2. 提供商类型选 **Anthropic**：
   - API 地址：`https://shannonapi.xyz`（**不带 `/v1`**，cherry-studio 会自动拼接 `/v1/messages`）
   - API Key：控制台创建的 Key
3. 保存后点 **检查** 按钮验证连通性
4. 在模型管理中添加 `claude-sonnet-5-5` 等模型
5. 助手设置里把默认模型切换为刚添加的模型

## 方式二：OpenAI 类型

1. 设置 → 模型服务 → 添加，类型选 **OpenAI**
2. API 地址：`https://shannonapi.xyz/v1`（**必须带 `/v1`**）
3. 添加模型：`gpt-5.6-sol`、`deepseek-v4-flash`、`glm-5.3-flash` 等

## 使用建议

- 轻任务（翻译、摘要、润色）配 DeepSeek / GLM / MiniMax 组模型，成本远低于 Claude。
- 长文档对话用 Claude Kiro 组的 `claude-sonnet-5-5`，1M 上下文基本不会截断。
- 知识库 + 长系统提示词场景，Anthropic 类型的 `cache_control` 会自动命中缓存，重复提问的读取价约为写入价的 1/10。

# Cline 接入 ShannonAPI

适用于 Cline（VS Code 扩展）。Roo-Code 界面几乎一致，见 [roo-code.md](roo-code.md)。

## 方式一：Anthropic 原生模式（推荐）

Tool Call、长上下文、流式稳定性最好，适合日常编程。

1. 点击 Cline 侧边栏的齿轮图标进入设置
2. **API Provider** 选择 `Anthropic`
3. 勾选 **Use custom base URL**（部分版本显示为 Custom Base URL）
4. Base URL 填写：

   ```
   https://shannonapi.xyz
   ```

   ⚠️ **不要加 `/v1`**。Cline 会自己请求 `/v1/messages`，加了反而 404。
5. API Key 填控制台创建的 Key
6. Model 填 `claude-sonnet-5-5`（日常）或 `claude-opus-5`（复杂任务）
7. 保存后随便发一条消息测试

## 方式二：OpenAI 兼容模式

1. **API Provider** 选择 `OpenAI Compatible`
2. Base URL 填写：

   ```
   https://shannonapi.xyz/v1
   ```

   ⚠️ **必须带 `/v1`**。
3. Model ID 填 `claude-sonnet-5-5` 或 `gpt-5.6-sol`，API Key 同上
4. 按需开启 streaming（流式输出）

## 排错

| 现象 | 原因与处理 |
| --- | --- |
| Connection error / Failed to fetch | 九成是 Base URL 问题：Anthropic 模式多打了 `/v1`，或 OpenAI 模式漏打 |
| API Error 404 | 同上，路径问题 |
| API Error 401 | Key 错误，回控制台重新创建，注意别多复制空格 |
| API Error 429 | 并发被限流，稍等重试；检查是否多个窗口同时跑大任务 |
| Tool Call 不触发 | 确认模型 ID 以[模型广场](https://shannonapi.xyz/model-plaza)为准；Claude 系列在 Anthropic 原生模式下工具调用支持最完整 |
| 长对话被截断 | Claude Kiro 组 `claude-sonnet-5-5` 为 1M 上下文，基本不会截断 |

更多通用问题见 [排错手册](troubleshooting.md)。

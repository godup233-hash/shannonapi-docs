# Roo-Code 接入 ShannonAPI

Roo-Code 是 Cline 的社区分支，提供商配置界面与 Cline 基本一致。

## Anthropic 原生模式（推荐）

1. 设置 → **API Provider** 选 `Anthropic`
2. 勾选 **Use custom base URL**
3. Base URL 填 `https://shannonapi.xyz`（**不带 `/v1`**，Roo-Code 自动拼 `/v1/messages`）
4. 填 API Key，Model 填 `claude-sonnet-5-5` 或 `claude-opus-5`

## OpenAI 兼容模式

1. **API Provider** 选 `OpenAI Compatible`
2. Base URL 填 `https://shannonapi.xyz/v1`（**必须带 `/v1`**）
3. Model ID 填 `claude-sonnet-5-5` / `gpt-5.6-sol` 等

## Roo-Code 特有提示

- **Profiles（配置档）**：可以给不同项目建不同 profile，一个用 `claude-opus-5` 跑硬任务，一个用 `claude-sonnet-5-5` 跑日常，Key 和 Base URL 互相隔离。
- **Modes（模式）**：Architect / Code / Ask 等模式可分别指定模型，建议 Code 模式用 sonnet，Architect 用 opus。
- 报错处理与 Cline 完全相同，见 [cline.md](cline.md#排错) 和 [排错手册](troubleshooting.md)。

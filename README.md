# ShannonAPI 接入指南

ShannonAPI（https://shannonapi.xyz）是一个多模型 API 网关：一个 API Key 接入 Claude、GPT、Gemini、DeepSeek、Kimi、GLM、Grok 等主流模型，同时兼容 **Anthropic Messages 原生协议** 与 **OpenAI Chat Completions 协议**，支持 Tool Call、流式输出与 Prompt Cache。

本仓库收录常见开源工具接入 ShannonAPI 的完整配置方法，重点解决三类高频问题：

- Base URL 到底带不带 `/v1`
- 超时 / 并发（429、timeout）
- Tool Call、长文本流式输出

## 端点

| 协议 | Base URL | 说明 |
| --- | --- | --- |
| Anthropic 原生（Messages API） | `https://shannonapi.xyz` | 客户端自行拼接 `/v1/messages`，**不要再加 `/v1`** |
| OpenAI 兼容 | `https://shannonapi.xyz/v1` | 标准 `/chat/completions`，**必须带 `/v1`** |

鉴权方式：Anthropic 协议用 `x-api-key` 请求头；OpenAI 协议用 `Authorization: Bearer <key>`。API Key 在 https://shannonapi.xyz 控制台创建。

## 30 秒验证连通性

Anthropic 原生协议：

```bash
curl https://shannonapi.xyz/v1/messages \
  -H "x-api-key: $SHANNON_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-sonnet-5-5",
    "max_tokens": 256,
    "messages": [{"role": "user", "content": "用一句话介绍 ShannonAPI"}]
  }'
```

OpenAI 兼容协议：

```bash
curl https://shannonapi.xyz/v1/chat/completions \
  -H "Authorization: Bearer $SHANNON_API_KEY" \
  -H "content-type: application/json" \
  -d '{
    "model": "gpt-5.6-sol",
    "messages": [{"role": "user", "content": "hello"}]
  }'
```

## 模型速览

可用模型与价格以[模型广场](https://shannonapi.xyz/model-plaza)实时页面为准。主要分组（按官方价倍率）：

| 分组 | 倍率 | 特点 | 代表模型 |
| --- | --- | --- | --- |
| Claude Kiro | 0.18x | 90% 高缓存命中、1M 超长上下文 | claude-sonnet-5-5、claude-opus-5、claude-haiku-5-5 |
| Claude-Pro | 0.37x | 满血防降智，支持全部最新模型 | claude-opus-5、claude-sonnet-5、claude-haiku-4-5 |
| Claude-MAX | 0.88x | 不降智官方满血 MAX | claude-opus-5-5、claude-sonnet-5-5 |
| ChatGPT-特价 | 0.17x | 高性价比 | gpt-5.6-luna、gpt-6-luna、gpt-5.6-terra |
| ChatGPT-Pro | 0.27x | 满血防降智 | gpt-6-astra、gpt-5.6-sol、gpt-6-sol |
| DeepSeek | 0.14x | 官方低谷价 | deepseek-v4-flash、deepseek-v4.1-flash |
| Kimi K3 | 0.12x | 低价 | kimi-k3 |
| MiniMax | 0.15x | 低价 | minimax-m3、minimax-m3.1-flash |
| 智谱 GLM | 0.2x | 低价 | glm-5.3、glm-5.3-flash |
| grok | 0.2x | — | grok-4.7、grok-4.6、grok-4.5 |
| gemini | 0.31x | — | gemini-3.8-flash |

计费说明：按实际用量计费，充值按 1:1 到账（充 ¥10 得 $10 额度）；输入、输出、缓存读取分别计价，支持 Prompt Cache（缓存读取价通常约为写入价的 1/10），详见模型广场。

## 工具接入索引

| 工具 | 文档 |
| --- | --- |
| Cline（VS Code） | [docs/cline.md](docs/cline.md) |
| Roo-Code（VS Code） | [docs/roo-code.md](docs/roo-code.md) |
| cherry-studio（桌面客户端） | [docs/cherry-studio.md](docs/cherry-studio.md) |
| zotero-pdf2zh / PDFMathTranslate（PDF 翻译） | [docs/zotero-pdf2zh.md](docs/zotero-pdf2zh.md) |
| VideoLingo（视频字幕翻译） | [docs/videolingo.md](docs/videolingo.md) |
| AstrBot（聊天机器人） | [docs/astrbot.md](docs/astrbot.md) |
| LangBot（聊天机器人） | [docs/langbot.md](docs/langbot.md) |
| 排错手册 | [docs/troubleshooting.md](docs/troubleshooting.md) |
| 协议细节与快速开始 | [docs/quick-start.md](docs/quick-start.md) |

## 选型建议

- **编程（Cline / Roo-Code）**：日常用 `claude-sonnet-5-5`（Claude Kiro 组，0.18x，1M 上下文）；复杂重构、长链路 Agent 任务上 `claude-opus-5`。
- **翻译（zotero-pdf2zh / VideoLingo）**：`deepseek-v4-flash`、`gpt-5.6-luna`、`minimax-m3.1-flash`，大批量处理成本极低；质量优先换 `claude-sonnet-5-5`。
- **聊天机器人（AstrBot / LangBot）**：默认 `claude-sonnet-5-5`，可按群组配不同模型；多个 Key 可做负载均衡。

## 说明

- 本文档为 ShannonAPI 社区接入指南，与上述开源项目官方无隶属关系；各工具配置界面随版本可能变化，请以你安装版本的文档为准。
- 价格、模型列表、分组随时可能调整，一切以 https://shannonapi.xyz/model-plaza 实时信息为准。
- 遇到接入问题，先查 [排错手册](docs/troubleshooting.md)。

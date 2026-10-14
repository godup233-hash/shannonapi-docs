# 排错手册

接入第三方 API 网关时，90% 的报错是 Base URL 路径问题。先对照这张表：

| 客户端使用的协议 | Base URL 正确写法 | 常见错法 |
| --- | --- | --- |
| Anthropic 原生（`/v1/messages`） | `https://shannonapi.xyz` | 多加 `/v1` → 404 |
| OpenAI 兼容（`/chat/completions`） | `https://shannonapi.xyz/v1` | 漏 `/v1` → 404 |

## 按报错查

### 401 / 403 Unauthorized

Key 不存在、被删除或过期。回 https://shannonapi.xyz 控制台重新创建；复制 Key 时注意别带上首尾空格或换行。

### 404 Not Found

Base URL 路径错误。对照上表检查 `/v1`；同时确认：

- 没有把 `https` 写成 `http`
- 域名后没有多带路径（如 `https://shannonapi.xyz/openai/v1` 是错的）

### 429 Too Many Requests

客户端并发超过渠道限额：

- 降低并发：VideoLingo 的 `max_workers`、zotero-pdf2zh 的翻译线程数、AstrBot/LangBot 的并行任务数
- 客户端加指数退避重试（多数工具自带）
- 多个工具同时跑大批量任务时，错峰执行

### timeout / connection error / ECONNRESET

- 长文本流式输出被客户端超时切断：调大客户端超时（LangBot `timeout`、自建脚本的 fetch/axios timeout）
- 网络层问题：确认代理、防火墙没有拦截；用 `curl -v https://shannonapi.xyz/v1/models` 测试基础连通性
- 模型不存在有时也会被客户端显示成连接错误：核对[模型广场](https://shannonapi.xyz/model-plaza)的模型 ID

### Tool Call 不生效 / 模型不调用工具

- 使用 Claude / GPT 系列支持 function calling 的模型（如 `claude-sonnet-5-5`、`gpt-5.6-sol`）
- 部分开源工具的 OpenAI 兼容模式会裁剪 tool 定义，换 Anthropic 原生模式（如 Cline 选 Anthropic + 自定义 Base URL）兼容性最好
- AstrBot / LangBot 中确认模型的「工具使用能力」已勾选

### Prompt Cache 不生效

- Anthropic 协议：`cache_control` 要打在每次请求都不变的前缀（system、长文档）上，且内容太短没有意义
- OpenAI 协议：缓存自动生效，重复请求相同前缀即命中，无需手动标记

### 输出被截断 / 上下文不够

- `max_tokens` 调大
- Claude Kiro 组模型（如 `claude-sonnet-5-5`）为 1M 上下文，长文档可直接传入

### 消耗比预期快

- 检查是否反复重试同一请求（客户端未做退避会放大计费）
- 长系统提示词的场景开启 Prompt Cache，缓存读取价约为写入价的 1/10
- 控制台可查看每条模型的使用记录，定位消耗来源

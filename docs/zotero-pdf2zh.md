# zotero-pdf2zh / PDFMathTranslate 接入 ShannonAPI

两个项目同源（zotero-pdf2zh 是 PDFMathTranslate 的 Zotero 插件版），翻译引擎都是 OpenAI 兼容接口，配置字段一致。

## zotero-pdf2zh：config.json

新建 `config.json`，在 Zotero 的翻译配置里引用该文件路径：

```json
{
  "NOTO_FONT_PATH": "./LXGWWenKai-Regular.ttf",
  "translators": [
    {
      "name": "openai",
      "envs": {
        "OPENAI_BASE_URL": "https://shannonapi.xyz/v1",
        "OPENAI_API_KEY": "sk-你的Key",
        "OPENAI_MODEL": "deepseek-v4-flash"
      }
    }
  ]
}
```

要点：

- `OPENAI_BASE_URL` **必须带 `/v1`**。
- 翻译场景推荐 `deepseek-v4-flash` / `minimax-m3.1-flash` / `gpt-5.6-luna`，大批量处理成本极低；质量优先换 `claude-sonnet-5-5`。
- 也可以用 `openailiked` 引擎，字段为 `OPENAILIKED_BASE_URL` / `OPENAILIKED_API_KEY` / `OPENAILIKED_MODEL`，地址同样带 `/v1`。
- v4.1.7 及以上版本可在插件「LLM API配置管理」里图形化新增：服务名随意，基础 URL 填 `https://shannonapi.xyz/v1`，保存后启用即可。
- 新版 PDF2ZH Next 引擎改用 `config.toml`，把其中翻译服务的 `base_url` 改为 `https://shannonapi.xyz/v1`。
- 翻译线程数保持默认即可；同时翻译大量 PDF 若遇到 429，把线程数降到 2–3。

## PDFMathTranslate（命令行）

`config.json` 用同样的 `translators` 结构；也可用环境变量：

```bash
export OPENAI_BASE_URL="https://shannonapi.xyz/v1"
export OPENAI_API_KEY="sk-你的Key"
export OPENAI_MODEL="deepseek-v4-flash"
pdf2zh -i input.pdf
```

（环境变量名以你安装版本的文档为准。）

## 排错

- **401**：Key 错误，回控制台重新创建。
- **404 / model not found**：检查 `OPENAI_BASE_URL` 是否带 `/v1`；模型 ID 以[模型广场](https://shannonapi.xyz/model-plaza)为准。
- **429**：降低翻译线程数，稍后重试。
- **漏译段落**：可尝试开启该服务的 `*_enable_json_mode`（如 `openai_enable_json_mode`）参数。

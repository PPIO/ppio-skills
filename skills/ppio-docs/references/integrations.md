# 集成指南

使用本文件作为集成路由入口，根据用户意图打开对应的分类文件。

最后验证：2026-03-12

## 通用配置（适用于所有集成）

- **Base URL**：`https://api.ppio.com/openai`
- **API Key**：从 https://ppio.com/settings/key-management 获取
- **模型**：使用 PPIO 模型名称（示例：`deepseek/deepseek-r1`）

## 分类映射

- 框架与 SDK：[integrations-frameworks.md](integrations-frameworks.md)
- 客户端与平台：[integrations-clients.md](integrations-clients.md)
- 可观测性、Agent 与训练：[integrations-observability-agents.md](integrations-observability-agents.md)

## 工具到分类映射

- `LangChain`、`LlamaIndex`、`OpenAI Agents SDK` -> `integrations-frameworks.md`
- `Cursor`、`Continue`、`Claude Code`、`Dify`、`LobeChat` -> `integrations-clients.md`
- `LiteLLM`、`Portkey`、`Langfuse`、`Browser Use`、`Skyvern`、`Axolotl` -> `integrations-observability-agents.md`


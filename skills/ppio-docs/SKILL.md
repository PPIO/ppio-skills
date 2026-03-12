---
name: ppio-docs
description: |
  PPIO 平台官方参考指南，覆盖 LLM API（兼容 OpenAI）、Agent 沙箱、GPU（实例与无服务器）、集成接入、鉴权、定价、限流与故障排查。适用于“如何在具体应用场景中集成 PPIO？”以及 PPIO 请求失败等常见问题。
---

# PPIO 平台参考

按以下流程处理：
1. 识别用户所属产品方向（模型服务、Agent 沙箱、GPU 容器、集成、故障排查）。
2. 使用 WebFetch 或等效 HTTP 读取工具，从下方映射表中获取所需的最少参考文件。
3. 将价格、模型可用性和服务状态视为动态数据，回答前先获取最新信息。
4. 尽量返回可直接复制粘贴的命令或代码片段。
5. 如果本地参考文件未涵盖所需内容，提供最接近的经验证指引，明确说明缺口，并指向官方实时文档。

## LLM(大语言模型) 快速参考

| 键 | 值 |
|-----|-------|
| **API Base URL** | `https://api.ppio.com/openai` |
| **鉴权方式** | `Authorization: Bearer <API_KEY>` |
| **获取 API Key** | https://ppio.com/settings/key-management |
| **模型目录** | https://ppio.com/ai-computing/llm-api |
| **定价** | https://ppio.com/pricing |
| **控制台** | https://ppio.com/console |

## 文档映射表

| 问题 | 读取此文件 | 实时文档 |
|----------|-----------|-----------|
| 快速入门 | [quick-start.md](https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-docs/references/quick-start.md) | - |
| LLM API 使用 | [llm-guide.md](https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-docs/references/llm-guide.md) | [API 参考](https://ppio.com/docs/models/reference-llm-create-chat-completion) |
| Agent 沙箱 | [sandbox-guide.md](https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-docs/references/sandbox-guide.md) | [文档](https://ppio.com/docs/sandbox/overview) |
| GPU 实例 | [gpu-guide.md](https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-docs/references/gpu-guide.md) | [文档](https://ppio.com/docs/gpu/gpu-function-overview) |
| 工具集成 | [integrations.md](https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-docs/references/integrations.md) | [文档](https://ppio.com/docs/third-party/overview) |
| 常见问题 | [faq-and-issues.md](https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-docs/references/faq-and-issues.md) | [FAQ](https://ppio.com/docs/gpu/faq) |

## 参考文件选择规则

- 初次配置和最简示例使用 `quick-start.md`（见上方映射表）。
- 聊天补全、工具调用、视觉、JSON 模式和批量任务使用 `llm-guide.md`（见上方映射表）。
- SDK/CLI、生命周期、模板和文件操作使用 `sandbox-guide.md`（见上方映射表）。
- 选择 GPU 实例与无服务器 GPU 以及定价权衡使用 `gpu-guide.md`（见上方映射表）。
- 集成问题统一以 `integrations.md`（见上方映射表）为入口，再从中选择子文档。
- 调试时优先使用 `faq-and-issues.md`（见上方映射表）。
- 如果无法从 GitHub 获取原始文件，回退到本技能目录中的本地 `references/*.md` 文件。
- 若答案涉及配额、定价、模型数量、延迟或硬件可用性，请先通过实时文档/API 验证，并在响应中注明验证日期。

## 动态数据策略

- 将模型目录大小、定价、限流、延迟和区域可用性视为动态数据。
- 在最终响应中以 `Verified on: YYYY-MM-DD` 格式标注动态数据的验证时间。
- 如果无法实时验证，明确说明数据可能已过期，并附上官方来源链接。

## 动态数据（始终获取最新值）

### 获取最新模型列表
```bash
curl https://api.ppio.com/openai/v1/models \
  -H "Authorization: Bearer $PPIO_API_KEY"
```

### 当前定价
访问 https://ppio.com/pricing 查看最新定价。

### 更新日志
- 模型 API 服务 发版记录: https://ppio.com/docs/announcement/changelog-llm
- GPU 容器服务 发版记录: https://ppio.com/docs/announcement/changelog-gpu
- Agent 沙箱 发版记录: https://ppio.com/docs/announcement/changelog-agent-sx

# 集成：客户端与平台

最后验证：2026-03-12

## 目录
- [AI 编程助手](#ai-编程助手)
- [AI 平台](#ai-平台)
- [浏览器扩展](#浏览器扩展)

## AI 编程助手

### Cursor
1. 打开 **Settings** -> **Models**
2. 取消勾选默认模型
3. 添加模型：`deepseek/deepseek-r1`
4. 设置 **OpenAI Base URL**：`https://api.ppio.com/openai`
5. 填入你的 **PPIO API Key**
6. 点击 **Verify**

### Continue（VS Code / JetBrains）

编辑 `~/.continue/config.json`：
```json
{
  "models": [
    {
      "title": "PPIO DeepSeek",
      "provider": "openai",
      "model": "deepseek/deepseek-r1",
      "apiBase": "https://api.ppio.com/openai",
      "apiKey": "<YOUR_API_KEY>"
    }
  ]
}
```

### Claude Code

```bash
export OPENAI_API_BASE=https://api.ppio.com/openai
export OPENAI_API_KEY=<YOUR_API_KEY>
```

### CodeCompanion（Neovim）

```lua
require("codecompanion").setup({
  adapters = {
    ppio = function()
      return require("codecompanion.adapters").extend("openai", {
        url = "https://api.ppio.com/openai/v1/chat/completions",
        env = { api_key = "PPIO_API_KEY" },
        schema = { model = { default = "deepseek/deepseek-r1" } },
      })
    end,
  },
})
```

## AI 平台

### Dify
1. 进入 **Settings** -> **Model Providers**
2. 在列表中找到 **PPIO**
3. 粘贴你的 API Key
4. 点击 **Save**

### LangFlow
1. 添加 **ChatOpenAI** 组件
2. 设置 **OpenAI API Base**：`https://api.ppio.com/openai`
3. 设置 **OpenAI API Key**：你的 PPIO Key
4. 设置 **Model Name**：`deepseek/deepseek-r1`

### AnythingLLM
1. 进入 **Settings** -> **LLM Preference**
2. 选择 **PPIO** 或 **Generic OpenAI**
3. 填入 API Key 和 Base URL
4. 选择模型

## 浏览器扩展

### LobeChat
1. 进入 **Settings** -> **Language Models**
2. 启用 **PPIO** 提供商
3. 填入 API Key
4. 选择模型

### ChatBox
1. 打开 **Settings**
2. 添加新 AI 提供商：**OpenAI API Compatible**
3. API Host：`https://api.ppio.com`
4. API Key：你的 PPIO Key
5. 模型：`deepseek/deepseek-r1`

### Page Assist
1. 点击扩展图标 -> **Settings**
2. 选择 **PPIO** 或 **Custom OpenAI**
3. 填入 API Key 和 Base URL

---
name: ppio-sandbox
description: 当用户提到「创建沙箱」「构建模板」「部署 agent」「列出沙箱」「杀掉沙箱」「沙箱日志」「克隆沙箱」「提交沙箱」「模板构建」「ppio-sandbox-cli」「ppio sandbox」「沙箱」或任何 PPIO Sandbox CLI 相关操作时触发。
---

# PPIO Agent Sandbox CLI

`ppio-sandbox-cli` 封装了 PPIO Agent Sandbox 平台。用它来从 Dockerfile 构建模板、管理沙箱生命周期、以及部署 Agent。

---

## 安装

如果本技能尚未安装到本地，`[AGENT]` 请按以下步骤安装。

### 检测 Agent 平台

```bash
# Claude Code
if [ -d "$HOME/.claude/skills" ]; then
  echo "CLAUDE_CODE"
# Gemini CLI
elif [ -d "$HOME/.gemini/skills" ]; then
  echo "GEMINI_CLI"
# OpenClaw
elif command -v openclaw >/dev/null 2>&1; then
  echo "OPENCLAW"
else
  echo "UNKNOWN"
fi
```

### 按平台安装

**Claude Code:**

```bash
SKILL_DIR="$HOME/.claude/skills/ppio-sandbox"
mkdir -p "$SKILL_DIR/references"
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/SKILL.md" \
  -o "$SKILL_DIR/SKILL.md"
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/references/cli-reference.md" \
  -o "$SKILL_DIR/references/cli-reference.md"
echo "已安装到 $SKILL_DIR"
```

**Gemini CLI:**

```bash
SKILL_DIR="$HOME/.gemini/skills/ppio-sandbox"
mkdir -p "$SKILL_DIR/references"
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/SKILL.md" \
  -o "$SKILL_DIR/SKILL.md"
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/references/cli-reference.md" \
  -o "$SKILL_DIR/references/cli-reference.md"
echo "已安装到 $SKILL_DIR"
```

**其他 Agent / 手动安装:**

```bash
SKILL_DIR="<你的-agent-skills-目录>/ppio-sandbox"
mkdir -p "$SKILL_DIR/references"
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/SKILL.md" \
  -o "$SKILL_DIR/SKILL.md"
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/references/cli-reference.md" \
  -o "$SKILL_DIR/references/cli-reference.md"
```

### 安装完成标准

安装未完成，除非以下所有条件均满足：

1. SKILL.md 已保存到本地技能目录
2. `references/cli-reference.md` 已保存到同级目录
3. CLI 已安装且为最新版本（下方 Step 0 检查通过）
4. 认证已验证
5. `[AGENT]` 向用户发送确认消息：

```
✅ ppio-sandbox 技能已安装。

你现在可以：
- 「创建沙箱」— 基于模板启动一个沙箱
- 「构建模板」— 从 Dockerfile 构建模板
- 「部署 agent」— 部署并调用 Agent
- 「列出沙箱」— 查看正在运行的沙箱

CLI 版本：<version>
文档：https://ppio.ai/docs/guides/sandbox-overview
```

---

## Step 0：确保 CLI 已安装且为最新版本

**每次触发技能时都执行此检查。**

```bash
if ! command -v node >/dev/null 2>&1; then
  echo "NO_NODE"
elif ! command -v ppio-sandbox-cli >/dev/null 2>&1; then
  echo "NOT_INSTALLED"
else
  _LOCAL=$(ppio-sandbox-cli --version)
  _LATEST=$(npm view ppio-sandbox-cli@beta version 2>/dev/null)
  if [ -n "$_LATEST" ] && [ "$_LOCAL" != "$_LATEST" ]; then
    echo "OUTDATED local=$_LOCAL latest=$_LATEST"
  else
    echo "OK $_LOCAL"
  fi
fi
```

根据结果执行：

- **NO_NODE** → 先安装 Node.js：
  - macOS：`brew install node`
  - Linux：`curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash - && sudo apt-get install -y nodejs`
- **NOT_INSTALLED** → `npm install -g ppio-sandbox-cli@beta`
- **OUTDATED** → `npm install -g ppio-sandbox-cli@beta`
- **OK** → 继续。

安装或升级后，验证认证状态：

```bash
ppio-sandbox-cli auth info 2>&1 || echo "NOT_LOGGED_IN"
```

如果 NOT_LOGGED_IN，运行 `ppio-sandbox-cli auth login`（会打开浏览器）。

设置 `PPIO_API_KEY` 环境变量供 SDK 使用。

## 快速参考

| 项目 | 值 |
|------|-----|
| **CLI 名称** | `ppio-sandbox-cli` |
| **文档** | https://ppio.ai/docs/guides/sandbox-overview |
| **控制台** | https://ppio.ai/console |
| **NPM** | https://www.npmjs.com/package/ppio-sandbox-cli |

## 命令总览

```
ppio-sandbox-cli
├── auth          # login, logout, info, configure（切换团队）
├── template      # build, list, init, delete, publish, unpublish, version
├── sandbox       # create, list, connect, kill, logs, metrics, clone, commit
└── agent         # configure, launch（别名：deploy，部署）, invoke
```

## 常用工作流

### 1. 从 Dockerfile 构建模板

```bash
# 初始化一个模板 Dockerfile
ppio-sandbox-cli template init

# 构建并推送（自动检测 ppio.Dockerfile）
ppio-sandbox-cli template build -n my-template

# 重新构建已有模板
ppio-sandbox-cli template build <template-id>
```

### 2. 创建和使用沙箱

```bash
# 创建沙箱但不连接终端（适用于非交互 / Agent 环境）
ppio-sandbox-cli sandbox create <template-id> --detach

# 创建沙箱并连接交互式终端（需要 TTY）
ppio-sandbox-cli sandbox create <template-id>

# 列出正在运行的沙箱
ppio-sandbox-cli sandbox list

# 连接到已有沙箱
ppio-sandbox-cli sandbox connect <sandbox-id>

# 查看日志（实时流式输出）
ppio-sandbox-cli sandbox logs <sandbox-id> -f

# 查看资源指标（CPU、内存、磁盘）
ppio-sandbox-cli sandbox metrics <sandbox-id> -f

# 终止沙箱
ppio-sandbox-cli sandbox kill <sandbox-id>

# 终止所有运行中的沙箱
ppio-sandbox-cli sandbox kill --all
```

**重要：** 在 AI Agent（Claude Code、Gemini CLI 等）中运行时，`sandbox create` 必须加 `--detach`（`-d`）。这些环境没有真实 TTY，交互式终端会失败。用 `--detach` 创建后，如需终端可在真正的终端中运行 `sandbox connect`。

### 3. 克隆与快照

```bash
# 克隆沙箱（创建相同副本）
ppio-sandbox-cli sandbox clone <sandbox-id> --count 3

# 将沙箱当前状态提交为快照模板
ppio-sandbox-cli sandbox commit <sandbox-id> --alias my-snapshot
```

### 4. 部署 Agent

```bash
# 配置 Agent 项目（创建 Dockerfile + 配置）
ppio-sandbox-cli agent configure -n my-agent -e app.py

# 部署到 PPIO Sandbox
ppio-sandbox-cli agent launch

# 调用已部署的 Agent（传入沙箱所需的环境变量）
ppio-sandbox-cli agent invoke '{"prompt": "hello"}' --stream --env PPIO_API_KEY=$PPIO_API_KEY
```

### 5. 模板管理

```bash
# 列出模板
ppio-sandbox-cli template list

# 发布（设为公开）
ppio-sandbox-cli template publish <template-id>

# 取消发布（设为私有）
ppio-sandbox-cli template unpublish <template-id>

# 查看版本列表及回滚
ppio-sandbox-cli template version <template-id>
ppio-sandbox-cli template version <template-id> --rollback <build-id>

# 删除
ppio-sandbox-cli template delete <template-id>
```

## 安全

- **API Key**：设置 `PPIO_API_KEY` 环境变量供 SDK 使用。调用 Agent 时需用 `--env PPIO_API_KEY=$PPIO_API_KEY` 显式传入 — 沙箱环境不会继承本地环境变量。切勿提交到 git — 使用 `.env` 或 shell profile。
- **认证令牌**：由 `ppio-sandbox-cli auth login` 存储在本地。运行 `auth logout` 可撤销。
- **镜像仓库凭据**：`template build` 的 `-u`/`-w` 参数用于私有 Docker 仓库。优先使用环境变量而非 CLI 参数，避免在 shell 历史中泄露密钥。

## 输出解读

| 命令 | 输出 | 关键字段 |
|------|------|----------|
| `template build` | 构建进度 → 模板 ID | Template ID（用于 `sandbox create`） |
| `template list` | 模板列表 | `ID`、`Name`、`Status`、`Type` |
| `sandbox create` | Sandbox ID（`--detach` 模式）或交互式终端 | Sandbox ID（用于 connect/kill/logs） |
| `sandbox list` | 运行中沙箱列表 | `ID`、`Template`、`State`、`Created` |
| `sandbox logs` | 实时日志流 | 时间戳、级别、消息 |
| `sandbox metrics` | CPU/内存/磁盘统计 | 百分比和绝对值 |
| `sandbox clone` | 新沙箱 ID 列表 | 每个克隆一个 ID |
| `sandbox commit` | 新快照模板 ID | Template ID（可像构建模板一样复用） |
| `agent launch` | 构建进度 → 部署 URL | Agent ID（`agent_name-template_id`） |
| `agent invoke` | Agent 响应（JSON 或流式） | 取决于 Agent 实现 |

## 注意事项

- 模板名称只允许小写字母、数字、短横线和下划线。
- `--memory-mb` 必须是偶数（默认：512）。
- `--cpu-count` 默认为 2。
- 不加 `--detach` 的 `sandbox create` 会自动连接终端 — 用 Ctrl+D 或 `exit` 断开。
- 在非 TTY 环境（AI Agent、CI/CD）中，必须使用 `sandbox create --detach`。
- `agent invoke` 运行在全新沙箱中 — 本地环境变量不会自动传入。使用 `--env KEY=VALUE` 显式传递。
- `agent launch` 超时默认 300 秒；大镜像可用 `--timeout` 增加。
- 配置存储在项目根目录的 `ppio.toml` 中（`template build` 后生成）。

## 故障排除

| 错误 | 原因 | 解决方案 |
|------|------|----------|
| `Error: not logged in` | 没有认证令牌 | 运行 `ppio-sandbox-cli auth login` |
| `Error: template not found` | ID 错误或已删除 | 运行 `template list` 确认；检查 `--team` 参数 |
| `Error: sandbox not found` | 沙箱已终止或过期 | 运行 `sandbox list` 检查；沙箱到期后会自动销毁 |
| `setRawMode is not a function` | 在非 TTY 环境中未加 `--detach` 运行 `sandbox create` | 使用 `sandbox create <template-id> --detach` |
| `EACCES` on `npm install -g` | 没有全局 npm 权限 | 使用 `sudo npm install -g` 或修正 npm 前缀（`npm config set prefix ~/.npm-global`） |
| 构建超时 | 大镜像或网络慢 | 增加 `--timeout`；用 `--no-cache` 跳过缓存层 |
| `--memory-mb` 校验错误 | 提供了奇数值 | 使用偶数（如 512、1024、2048） |
| `connect` 卡住 | 沙箱仍在启动 | 等待就绪；用 `sandbox logs` 查看启动错误 |
| `agent invoke` 返回 404 | Agent 未部署或 ID 错误 | 用 `agent launch --dry-run` 验证；检查 Agent ID 格式：`name-templateId` |

## 完整 CLI 参考

当需要查看某个命令的完整选项时，Read 'references/cli-reference.md' 获取所有命令的详细参数说明。

---

## 更新

更新本技能到最新版本：

```bash
curl -sL "https://raw.githubusercontent.com/PPIO/ppio-skills/main/skills/ppio-sandbox/SKILL.md" \
  -o "$(dirname "$0")/SKILL.md" 2>/dev/null || echo "请从 https://github.com/PPIO/ppio-skills 手动更新"
```

仅在用户明确要求时更新。

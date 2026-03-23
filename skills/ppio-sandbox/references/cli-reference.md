# PPIO Sandbox CLI — 完整命令参考

## auth

### `auth login`
通过浏览器 OAuth 登录。凭据存储在本地。

### `auth logout`
删除本地存储的凭据。

### `auth info`
显示当前用户邮箱、团队名称和团队 ID。

### `auth configure`
交互式选择切换可用团队。

---

## template（别名：tpl）

### `template build [template-id]`（别名：bd）
从 Dockerfile 构建沙箱模板。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-p, --path <path>` | 根目录 | `.` |
| `-d, --dockerfile <file>` | Dockerfile 路径 | 自动检测 |
| `-n, --name <name>` | 模板名称（小写字母/数字/短横线/下划线） | — |
| `-c, --cmd <command>` | 沙箱启动命令 | — |
| `--ready-cmd <command>` | 就绪检查（必须返回 exit 0） | — |
| `-i, --image <image>` | 使用预构建镜像代替 Dockerfile | — |
| `-u, --username <user>` | 镜像仓库用户名 | — |
| `-w, --password <pass>` | 镜像仓库密码 | — |
| `--team <team-id>` | 团队 ID | — |
| `--config <file>` | 配置文件路径 | — |
| `--cpu-count <n>` | CPU 数量 | `2` |
| `--memory-mb <n>` | 内存（MB，必须为偶数） | `512` |
| `--build-arg <K=V...>` | Docker 构建参数 | — |
| `--no-cache` | 跳过构建缓存 | — |

如果提供了 `[template-id]`，则重新构建该模板。否则创建新模板。

### `template list`（别名：ls）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--team <team-id>` | 按团队筛选 | — |
| `-ty, --type <type>` | `template_build` 或 `snapshot_template` | `template_build` |
| `-p, --page <n>` | 页码（从 1 开始） | `1` |
| `-l, --limit <n>` | 每页数量 | `10` |

### `template init`（别名：it）
在当前或指定目录创建模板 `ppio.Dockerfile`。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-p, --path <path>` | 根目录 | `.` |

### `template delete [template-id]`（别名：dl）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-p, --path <path>` | 根目录 | — |
| `--config <file>` | 配置文件路径 | — |
| `-s, --select` | 交互式选择模式 | — |
| `--team <team-id>` | 团队 ID | — |
| `-y, --yes` | 跳过确认 | — |

### `template publish [template-id]`（别名：pb）
将模板设为公开。选项同 `delete`。

### `template unpublish [template-id]`（别名：upb）
将模板设为私有。选项同 `delete`。

### `template version [template-id]`（别名：vn）
列出所有构建版本或回滚到特定版本。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-p, --path <path>` | 根目录 | — |
| `--config <file>` | 配置文件路径 | — |
| `-r, --rollback <build-id>` | 回滚到指定构建 | — |

---

## sandbox（别名：sbx）

### `sandbox create [template-id]`（别名：cr）
创建沙箱并连接终端。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-p, --path <path>` | 根目录 | — |
| `--config <file>` | 配置文件路径 | — |
| `-d, --detach` | 创建沙箱但不连接终端 | — |

如未指定模板 ID，则使用 `ppio.toml` 中的配置。

### `sandbox list`（别名：ls）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-s, --state <states>` | 按状态筛选（逗号分隔：running, paused） | `running` |
| `-m, --metadata <k=v>` | 按元数据筛选 | — |
| `-l, --limit <n>` | 最大返回数量 | — |

### `sandbox connect <sandboxID>`（别名：cn）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--timeout <seconds>` | 连接超时 | `300` |

### `sandbox kill [sandboxID]`（别名：kl）

| 选项 | 说明 |
|------|------|
| `-a, --all` | 终止所有运行中的沙箱 |

互斥：指定沙箱 ID 或使用 `--all`。

### `sandbox logs <sandboxID>`（别名：lg）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--level <level>` | DEBUG, INFO, WARN, ERROR | `INFO` |
| `-f, --follow` | 实时流式输出 | — |
| `--format <fmt>` | `pretty` 或 `json` | `pretty` |
| `--loggers [names]` | 按 logger 名称筛选（逗号分隔） | — |

### `sandbox metrics <sandboxID>`（别名：mt）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-f, --follow` | 实时流式输出 | — |
| `--format <fmt>` | `pretty` 或 `json` | `pretty` |

报告 CPU、内存和磁盘使用情况。

### `sandbox clone <sandboxID>`（别名：cl）

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-c, --count <n>` | 克隆数量 | `1` |
| `-t, --timeout <seconds>` | 克隆超时 | 继承父沙箱 |
| `-n, --nodeid <id>` | 调度到指定节点 | — |
| `-s, --strict` | 要求精确数量否则失败 | — |

### `sandbox commit <sandboxID>`（别名：cm）
从当前沙箱状态创建快照模板。

| 选项 | 说明 |
|------|------|
| `-a, --alias <alias>` | 创建的模板别名 |

---

## agent

### `agent configure`
设置 Agent 项目配置，创建 Dockerfile 和 docker-ignore。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `-n, --name <name>` | Agent 名称 | 自动检测 |
| `-e, --entrypoint <file>` | 入口文件 | 自动检测或 `app.py` |
| `--agent-version <ver>` | Agent 版本 | `1.0.0` |
| `-a, --author <email>` | 作者邮箱 | 从环境或提示获取 |
| `-rf, --requirements-file <file>` | 依赖文件路径 | — |
| `--no-interactive` | 跳过交互式提示 | — |
| `--force` | 强制覆盖配置 | — |
| `--verbose` | 详细输出 | — |

### `agent launch`（别名：deploy）
构建并部署 Agent 到 PPIO Sandbox。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--timeout <seconds>` | 部署超时 | `300` |
| `--no-cache` | 禁用构建缓存 | — |
| `--dry-run` | 试运行，不实际部署 | — |
| `--update-existing` | 更新已有模板 | — |
| `--verbose` | 详细输出 | — |

### `agent invoke <payload>`
使用 JSON 负载或提示文本调用已部署的 Agent。

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `--agentId <id>` | Agent ID（`agent_name-template_id`） | — |
| `--stream` | 启用流式响应 | — |
| `--timeout <seconds>` | 请求超时 | `60` |
| `--env <key=value>` | 环境变量（可重复使用） | — |
| `--verbose` | 详细输出 | — |

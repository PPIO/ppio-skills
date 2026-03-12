# PPIO Skills

PPIO 官方 Skills 仓库。
本仓库托管可复用的技能文件（`SKILL.md`），适用于支持技能工作流的 Agent 生态系统。

## 从本仓库安装技能

使用以下命令安装：

```bash
npx skills add PPIO/ppio-skills --skill ppio-docs
```

可用的 `--skill` 值请参见下方「可用技能」列表。

安装完成后，如有需要请重启你的 Agent 运行时。

## 可用技能

在 `--skill` 中使用以下名称：

- `ppio-docs`：PPIO 平台文档与集成参考技能


## 贡献流程

1. Fork 本仓库并创建分支。
2. 在 `skills/<skill-name>/` 目录下添加或更新技能。
3. 提交 PR，并说明使用场景、触发词及验证步骤。
4. 经审核通过后合并。

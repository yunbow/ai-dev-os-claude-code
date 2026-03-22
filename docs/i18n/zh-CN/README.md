# AI Dev OS Plugin — Claude Code

[![Lint & Link Check](https://github.com/yunbow/ai-dev-os-plugin-claude-code/actions/workflows/lint.yml/badge.svg)](https://github.com/yunbow/ai-dev-os-plugin-claude-code/actions/workflows/lint.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](../../../LICENSE)

将 **AI Dev OS** 的4层模型作为 Claude Code 的 Skills / Hooks / Sub-agents 来执行的插件。

**[AI Dev OS](https://github.com/yunbow/ai-dev-os) 生态系统的一部分** — 将隐性知识转化为可执行的 AI 编码规则的框架（[Lifespan Layers](https://github.com/yunbow/ai-dev-os#lifespan-layers--the-4-layer-model)）。
需要在项目中设置 [AI Dev OS Rules](https://github.com/yunbow/ai-dev-os-rules-typescript)。

## 为什么选择这个插件？

将 AI Dev OS 指南转化为 Claude Code 的**自动化工作流**：

- **11 Skills** — `/ai-dev-os-check`、`/ai-dev-os-scan`、`/ai-dev-os-extract` 等
- **3 Sub-agents** — 哲学顾问、原则检查器、指南审计员
- **Lifecycle Hooks** — 代码变更时自动检查、提交前提醒
- **一条命令安装** — `npx ai-dev-os init --rules typescript --plugin claude-code`

![AI Dev OS Check & Fix Report](../../../docs/images/ai-dev-os-check.png)

## 快速开始

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

> 自动添加 submodule、复制 CLAUDE.md 模板、合并 hooks。
> 详见 [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli)。

需要 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0 以及项目中已设置 AI Dev OS 层文件（L1-L3）（[TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)）。

<details>
<summary>手动设置</summary>

```bash
# 1. 将 AI Dev OS 规则添加为 submodule
git submodule add https://github.com/yunbow/ai-dev-os-rules-typescript.git docs/ai-dev-os
# Python 项目：
# git submodule add https://github.com/yunbow/ai-dev-os-rules-python.git docs/ai-dev-os

# 2. 将此插件添加为 submodule
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

1. 将 `hooks/hooks.json` 中的钩子复制到项目的 `.claude/settings.json`
2. 在 **Claude Code 聊天**中运行 `/ai-dev-os-init` 设置4层结构
3. 开始编码 — 钩子会自动引导你

详见[操作指南](./docs/operation-guide.md)。

</details>

## Skills（技能）

| 技能 | 命令 | 说明 |
|------|------|------|
| **Init** | `/ai-dev-os-init` | 初始化向导 — 30分钟内为项目引入 AI Dev OS |
| **Check** | `/ai-dev-os-check` | 指南合规检查（支持 `git diff`、`--staged`、分支比较） |
| **Scan** | `/ai-dev-os-scan` | 对所有源文件进行项目级全面合规扫描 |
| **Review** | `/ai-dev-os-review` | PR 前自审 — 整合 L3 合规 + L2 设计审查 + L1 对齐 |
| **Extract** | `/ai-dev-os-extract` | 从代码审查差异中提取新规则（规则收割） |
| **Why** | `/ai-dev-os-why` | 追溯规则原理 L3→L2→L1 |
| **Plan** | `/ai-dev-os-plan` | 编码前创建包含指南检查清单的实现计划 |
| **Ticket** | `/ai-dev-os-ticket` | 生成包含实现摘要和检查清单候选的工单（本地文件或 GitHub Issue） |
| **Audit** | `/ai-dev-os-audit` | 审计4层健康状态：依赖规则、时效性、覆盖率、一致性 |
| **Evolve** | `/ai-dev-os-evolve` | 分析最近的提交，提议 L1-L2 更新（SECI螺旋） |
| **Report** | `/ai-dev-os-report` | 为团队和利益相关者生成合规摘要报告 |

## Sub-agents（子代理）

| 代理 | 模型 | 作用 |
|------|------|------|
| `philosophy-advisor` | Opus | 基于L1哲学的架构决策支持 |
| `principle-checker` | Sonnet | 验证代码变更与L2原则的一致性 |
| `guideline-auditor` | Sonnet | 审计L3指南的覆盖率、一致性和时效性 |

## Hooks（钩子）

| 事件 | 触发器 | 动作 |
|------|--------|------|
| PreToolUse (Write/Edit) | 代码变更时 | 轻量级指南合规检查 |
| PreToolUse (Bash: git commit) | 提交前 | 提醒运行 `/ai-dev-os-check` |
| PostToolUse (Write/Edit) | L1-L2文件编辑后 | 依赖规则违反警告 |

<details>
<summary>包结构</summary>

```text
ai-dev-os-plugin-claude-code/
├── .claude-plugin/
│   └── plugin.json              # 插件清单
├── skills/
│   ├── ai-dev-os-init/          # 初始化向导
│   ├── ai-dev-os-check/         # 指南合规检查（git diff）
│   ├── ai-dev-os-scan/          # 项目级全面合规扫描
│   ├── ai-dev-os-review/        # PR前自审（L1-L3）
│   ├── ai-dev-os-extract/       # 规则收割
│   ├── ai-dev-os-why/           # 规则原理说明（L3→L2→L1）
│   ├── ai-dev-os-audit/         # 4层健康审计
│   ├── ai-dev-os-plan/           # 指南感知的实现规划
│   ├── ai-dev-os-ticket/        # 工单生成（实现摘要 + 检查清单候选）
│   ├── ai-dev-os-evolve/        # SECI螺旋反馈（L4→L1）
│   └── ai-dev-os-report/        # 合规报告生成
├── agents/
│   ├── philosophy-advisor.md     # 基于L1哲学的架构判断
│   ├── principle-checker.md      # L2原则一致性验证
│   └── guideline-auditor.md      # L3指南审计
├── hooks/
│   └── hooks.json                # 生命周期事件钩子
├── templates/
│   ├── CLAUDE.md.template        # CLAUDE.md模板
│   ├── ai-dev-os-starter/        # 最小配置模板
│   └── ai-dev-os-full/           # 完整4层模板
└── settings.json                  # 默认权限设置
```

</details>

## 优先级级联

当规则冲突时：框架特定 > 通用 > 项目特定 > 决策标准 > 哲学。[→ 详情](https://github.com/yunbow/ai-dev-os/blob/main/spec/priority-cascade.md)

## 相关

| 仓库 | 说明 |
|---|---|
| [ai-dev-os](https://github.com/yunbow/ai-dev-os) | Framework specification and theory |
| [rules-typescript](https://github.com/yunbow/ai-dev-os-rules-typescript) | TypeScript / Next.js / Node.js guidelines |
| [rules-python](https://github.com/yunbow/ai-dev-os-rules-python) | Python / FastAPI guidelines |
| [plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro) | Steering Rules and Hooks for Kiro |
| [plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor) | Cursor Rules (.mdc) |
| [cli](https://github.com/yunbow/ai-dev-os-cli) | `npx ai-dev-os init` |
| [benchmark](https://github.com/yunbow/ai-dev-os-benchmark) | Quantitative benchmark — guideline impact data |

## 许可证

[MIT](../../../LICENSE)

---

Languages: [English](../../../README.md) | [日本語](../ja/README.md) | 简体中文 | [한국어](../ko/README.md) | [Español](../es/README.md)

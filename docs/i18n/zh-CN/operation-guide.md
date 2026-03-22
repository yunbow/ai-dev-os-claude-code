# AI Dev OS Plugin — Claude Code — 操作指南

## 目录

1. [前提条件](#1-前提条件)
2. [安装](#2-安装)
3. [初始设置](#3-初始设置)
4. [日常工作流](#4-日常工作流)
5. [定期维护](#5-定期维护)
6. [团队引导](#6-团队引导)
7. [语言策略](#7-语言策略)
8. [CI 集成](#8-ci-集成)
9. [故障排除](#9-故障排除)

---

## 1. 前提条件

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0
- 项目中已设置 AI Dev OS 层文件（L1-L3）（模板：[TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)）

---

## 2. 安装

### 方式A：Git Submodule（推荐）

将 AI Dev OS 的更新与项目分开管理：

```bash
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

### 方式B：直接克隆

```bash
git clone https://github.com/yunbow/ai-dev-os-plugin-claude-code.git
cp -r ai-dev-os-plugin-claude-code/skills/ .claude/skills/
cp -r ai-dev-os-plugin-claude-code/agents/ .claude/agents/
```

### 钩子设置

将 `hooks/hooks.json` 中的钩子定义复制到项目的 `.claude/settings.json`：

```json
{
  "permissions": { ... },
  "hooks": {
    "PreToolUse": [ ... ],
    "PostToolUse": [ ... ],
    "UserPromptSubmit": [ ... ]
  }
}
```

完整的钩子配置请参考 `hooks/hooks.json`。

#### 钩子合并指南

如果 `.claude/settings.json` 已经存在并包含其他设置，请手动合并钩子：

#### 方法A：手动合并

1. 并排打开 `hooks/hooks.json` 和 `.claude/settings.json`
2. 从 `hooks/hooks.json` 复制每个数组（`PreToolUse`、`PostToolUse`、`UserPromptSubmit`）
3. 如果 `settings.json` 已有 `hooks` 键，将条目**追加**到现有数组
4. 如果 `settings.json` 没有 `hooks` 键，添加整个 `hooks` 对象

**验证**：合并后，确认 `settings.json` 的 `hooks` 键下包含 `PreToolUse`、`PostToolUse` 和 `UserPromptSubmit` 数组。

---

## 3. 初始设置

### 运行设置向导

```text
/ai-dev-os-init [tech-stack]
```

示例：

```text
/ai-dev-os-init nextjs
```

向导将会：

1. 询问你的技术栈、项目规模和现有规则文件
2. 检测并导入现有规则（`.cursorrules`、`CLAUDE.md`、`.eslintrc`）
3. 生成4层目录结构
4. 创建包含指南引用和优先级级联的 `CLAUDE.md`
5. 可选设置 git submodule 隔离

### 验证设置

初始化后，确认以下结构：

```text
ai-dev-os/
├── 01_philosophy/
├── 02_decision-criteria/
├── 03_guidelines/
│   ├── common/
│   └── frameworks/
└── 04_ai-frames/
```

---

## 4. 日常工作流

### 4.1 实现前规划

对于重要的更改，先创建指南感知的计划：

```text
/ai-dev-os-plan 添加JWT用户认证
```

这将会：

1. 分析你的请求并识别受影响的文件
2. 将文件映射到相关的 AI Dev OS 指南
3. 生成适用规则的检查清单
4. 在编写任何代码之前展示计划供你批准

当钩子检测到与实现相关的提示时，会建议使用 `/ai-dev-os-plan`。

### 4.2 创建工单而非直接实现

当你想推迟实现时（例如分配给团队成员或进行待办管理），可以创建工单：

```text
/ai-dev-os-ticket 添加JWT用户认证
```

这将会：

1. 执行与 `/ai-dev-os-plan` 相同的分析（受影响文件、指南映射、检查清单）
2. 询问工单的输出目标（如果 CLAUDE.md 中未配置）：
   - **本地文件**：在指定目录中保存为 `TICKET-001-add-user-auth.md`
   - **GitHub Issue**：通过 `gh issue create` 创建 Issue
3. 展示预览供你批准
4. 创建工单 — **不编写任何代码**

要预先配置工单输出目标，在项目的 `CLAUDE.md` 中添加：

```markdown
## Ticket Settings
- output: file
- path: docs/tickets/phase1
```

GitHub Issue 的情况：

```markdown
## Ticket Settings
- output: github-issue
```

### 4.3 编写代码

照常编写代码。钩子会自动：

- 在每次 Write/Edit 操作时检查指南合规性
- 编辑 L1-L2 文件时警告依赖规则违反
- 在提交前提醒运行合规检查

### 4.4 提交前

运行合规检查：

```text
/ai-dev-os-check
```

这将会：

1. 解析 `CLAUDE.md` 以找到适用的指南
2. 从 `git diff` 获取变更文件
3. 将文件映射到相关指南
4. 检查合规性并输出报告

### 4.5 代码审查后

当你在审查中修复了 AI 生成的代码时，提取新规则：

```text
/ai-dev-os-extract [file-path]
```

这将会：

1. 分析差异以识别*为什么*进行了更改
2. 以 `MUST` / `MUST NOT` 格式生成规则候选
3. 提议目标指南文件和 L2 原则链接
4. 批准后将规则添加到指南

### 4.6 理解规则

当团队成员对规则有疑问时：

```text
/ai-dev-os-why [rule-or-guideline]
```

示例：

```text
/ai-dev-os-why "为什么禁止使用 any 类型？"
```

---

## 5. 定期维护

### 每月：健康审计

```text
/ai-dev-os-audit
```

检查项目：

- 依赖规则合规性（L1 中无工具特定术语，L2 中无框架详情）
- 时效性（L1：5年，L2：3年，L3：12个月，L4：4个月）
- 可追溯性（L3→L2 链接，孤立规则）
- 覆盖率（所有文件模式都有指南）
- 一致性（无矛盾规则）

### 每季度：SECI 螺旋进化

```text
/ai-dev-os-evolve
```

分析最近的提交和审查模式，提议更新 L1 哲学和 L2 原则。这是"知识螺旋"——从日常实践中提取隐性知识转化为显性原则。

### 每周/每月：合规报告

```text
/ai-dev-os-report 1w
/ai-dev-os-report 1m
```

生成适合团队会议或利益相关者更新的摘要报告。

---

## 6. 团队引导

### 新成员入门

1. 阅读 `ai-dev-os/01_philosophy/core-values.md`（5分钟）
2. 浏览 `ai-dev-os/02_decision-criteria/`（10分钟）
3. 对不清楚的规则运行 `/ai-dev-os-why`
4. 开始编码——钩子会自动引导合规

### 团队采用 AI Dev OS

1. 在项目上运行 `/ai-dev-os-init`
2. 从 **starter** 模板（仅 L3）开始
3. 每次代码审查后使用 `/ai-dev-os-extract`
4. 2-4周后运行 `/ai-dev-os-audit` 识别差距
5. 通过 `/ai-dev-os-evolve` 逐步构建 L1-L2

---

## 7. 语言策略

插件的所有源文件（skills、agents、hooks、templates）均以**英文**维护。这是为了确保 LLM 的最佳精度和广泛的可访问性。翻译文档在 `docs/i18n/` 下作为参考提供，但英文是唯一的事实来源（Single Source of Truth）。

---

## 8. CI 集成

钩子在开发过程中提供实时建议，但仅在 AI 编码工具内运行。要在团队层面强制合规，请将 AI Dev OS 检查集成到 CI 流水线中。

### 强制级别

| 级别 | 行为 | 推荐对象 |
|------|------|---------|
| **建议性** | 钩子在开发中警告，CI 报告违规但不阻止 | 个人开发者、小团队 |
| **警告** | CI 在 PR 上发布违规评论但允许合并 | 逐步采用 AI Dev OS 的团队 |
| **阻止** | CI 在关键违规（安全、认证）时失败 | 拥有成熟规则的成熟团队 |

从建议性级别开始，随着规则成熟逐步升级（`[draft]` → `[proven]`）。

---

## 9. 故障排除

### 钩子未触发

- 确认钩子在 `.claude/settings.json` 中（而非直接读取 `hooks/hooks.json`）
- 检查 `jq` 是否已安装（`jq --version`）
- 在 Windows 上，确保 bash 在 PATH 中

### 找不到技能

- 确认技能文件在 `.claude/skills/` 目录中
- 每个技能必须有带有效 YAML frontmatter 的 `SKILL.md` 文件
- 运行 `ls .claude/skills/` 确认

### 误报违规

- 如果检查过于严格，编辑 `03_guidelines/` 中对应的指南
- 删除前使用 `/ai-dev-os-why` 了解规则的根据
- 考虑放宽规则而非完全删除

### 性能

- 钩子检查在每次 Write/Edit 时运行——如果太慢，缩小指南范围
- 对重型操作使用 `context: fork` 技能（extract、audit、evolve）
- 子代理在隔离上下文中运行，不会污染主会话

---

Languages: [English](../../operation-guide.md) | [日本語](../ja/operation-guide.md) | 简体中文 | [한국어](../ko/operation-guide.md) | [Español](../es/operation-guide.md)

# Micropowers — 开发文档

## 仓库架构

```
micropowers/
├── .dev_docs/                  # 设计文档（本地，不跟踪）
├── examples/                    # 使用示例
├── hooks/                       # session-start 等钩子
├── skills/                      # 各 skill 定义
│   ├── micropowers-brainstorm/     # Socratic 对齐
│   ├── micropowers-execute/            # 并行派发 + 执行纪律
│   ├── micropowers-finish/               # 独立收尾
│   ├── micropowers-plan/             # 精确 plan
│   └── micropowers/             # 入口：检测 + 路由
├── .worktrees/                  # git worktree 工作目录（已 gitignore）
│   └── dev/                     # dev 分支工作目录（当前）
├── .gitignore
├── INSTALL.md
├── README.md                     # 英文版（默认）
└── README_zh.md                  # 简体中文版
```

## 分支策略

| 分支 | 用途 | 说明 |
|------|------|------|
| `main` | 稳定发布 | 当前目录，只接受来自 dev 的合并。生产可用。 |
| `dev` | 日常开发 | `.worktrees/dev/`，所有变更在此进行。 |

**协作规则：**
- `main` 不直接修改。所有开发在 `dev` 分支进行。
- Dev 完成后，在 `main` 目录 `git merge dev` 发布。
- Worktree 方式保证 `main` 目录始终干净，可随时切换上下文。

## 开发工作流

```bash
# 当前在 dev 分支（.worktrees/dev/）
cd .worktrees/dev/

# 修改代码
# ... 编辑文件 ...

# 提交
git add .
git commit -m "feat: xxx"

# 推送到 dev
git push origin dev

# 合并到 main（在根目录执行）
cd /home/pax/coding/brainstorming/micropowers
git merge dev
```

## 编码规范

### Skill 开发

每个 skill 放在 `skills/<name>/SKILL.md`，遵循以下结构：

```markdown
---
name: <skill-name>
description: <一句话描述>
---

# Skill Name

## Overview

## Steps

## Quick Reference

## Common Mistakes

## Red Flags
```

### 提交信息格式

```
<type>: <简短描述>

<可选详细说明>
```

类型：`feat` / `fix` / `docs` / `refactor` / `style` / `chore`

## 质量要求

1. **Plan 先行** — 复杂变更前先写 plan，经过审视再动手
2. **自检闭环** — 修改后 review 自己的变更，确保无遗漏
3. **不破坏 main** — Dev 分支验证通过后才合并
4. **最小变更** — 只做必要修改，不顺手重构无关代码

## 研究对象

以下仓库克隆至 `../../research/`，作为架构与流程设计的参考对象。

| 项目 | 路径 | 研究方向 | 研究报告（已对齐最新版） |
|------|------|----------|----------|
| [obra/superpowers](https://github.com/obra/superpowers) | `research/superpowers/` | Agent 协作框架的完整参考实现。micropowers 的设计理念继承自 superpowers，需持续跟踪其架构演进。重点：skill 系统、路由机制、review 流程。 | [superpowers.md](.dev_docs/research/superpowers.md) — 基于 v6.1.1（14 skills） |
| [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) | `research/OpenSpec/` | Open Source Spec — 用 spec 驱动 AI 生成。重点：Spec 格式设计、验证机制、并行生成策略。与 micropowers 的 plan + Change 字段设计直接相关。 | [openspec.md](.dev_docs/research/openspec.md) — 基于 5956a8e |
| [vinvcn/mattpocock-skills-zh-CN](https://github.com/vinvcn/mattpocock-skills-zh-CN) | `research/mattpocock-skills-zh-CN/` | Matt Pocock 的 TypeScript skill 中文翻译。重点：skill 编写范式、文档结构、中文适配方式。 | [mattpocock-skills.md](.dev_docs/research/mattpocock-skills.md) — 基于 2999252 |

### 查阅方式

```bash
# 快速查看某个研究项目的顶层结构
ls ~/coding/research/<project-name>

# 读取具体文档
cat ~/coding/research/<project-name>/AGENTS.md
```

## 相关文档

- [设计文档](.dev_docs/design.md) — 项目设计理念与质量模型
- [INSTALL.md](INSTALL.md) — 安装指南
- [使用示例](examples/simple-feature.md) — 完整流程示例
- [研究项目报告](.dev_docs/research/) — 三个参考仓库的研究报告（superpowers / OpenSpec / mattpocock-skills）

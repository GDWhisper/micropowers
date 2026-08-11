# Micropowers — 开发文档

## 当前状态（2026-08-10）

| 项目 | 状态 |
|------|------|
| 版本 | `0.1.0`（版本号唯一来源：`package.json`） |
| npm | 已发布 [`micropowers`](https://www.npmjs.com/package/micropowers)@0.1.0 |
| git tag | `v0.1.0`（= `main` 的 `5a18558`） |
| pi 生态 | 正式 pi package —— `package.json` 里有 `pi.skills` manifest + `pi-package` keyword |
| 分支 | 只有 `main`（已推送远端）；早期的 `dev` / `test` 遗留分支已删除 |

### 分发形态

本仓库同时是三种东西，一份内容三种消费方式：

| 方式 | 命令 | 落地位置 |
|------|------|----------|
| `npx skills`（README 首推） | `npx skills add GDWhisper/micropowers -s '*'` | 复制到 `~/.agents/skills/` 等 agent 目录 |
| pi package | `pi install npm:micropowers` | `~/.pi/agent/npm/node_modules/micropowers/`，由 pi 加载，不复制 |
| 手工复制 | 见 [INSTALL.md](INSTALL.md) | 用户指定目录 |

**三种方式互斥，不能叠加。** pi 里同名 skill 先到先得，且自动发现目录（`~/.agents/skills/`、`~/.pi/agent/skills/`）的优先级**高于**包 —— 所以复制方式留下的副本会静默屏蔽 pi 包里的 skill，症状是「装了 pi 包但好像没生效」。切换方式前必须先删干净旧副本。

### skill 内部的路径约定

skill 引用同级资源（例如 `micropowers-styles/<name>.md`）必须写成**相对该 SKILL.md 自身目录**的路径，不能硬编码 `~/.agents/skills/...`：pi 包装完之后真实路径在 `~/.pi/agent/npm/node_modules/micropowers/skills/...` 之下，硬编码路径和从 cwd 出发的 `**/` Glob 都会失效。

## 仓库架构

```
micropowers/
├── examples/
│   └── simple-feature.md        # 完整流程示例
├── hooks/
│   └── session-start            # Claude Code / Cursor 用的注入钩子；pi 不用它（pi 自带 skill 索引）
├── skills/
│   ├── micropowers/             # 入口：续档检测 + 风格选择 + 路由
│   │   ├── SKILL.md
│   │   └── micropowers-styles/  # 四种协作风格，按需单个加载
│   ├── micropowers-brainstorm/  # Socratic 对齐（含可选的视觉伴侣 scripts/）
│   ├── micropowers-plan/        # 精确 plan + 验收清单
│   ├── micropowers-execute/     # 并行派发 + 执行纪律
│   └── micropowers-finish/      # 收尾
├── agents.md                    # 本文件：开发文档
├── INSTALL.md                   # 安装指南（也供 agent 直接 fetch 执行）
├── LICENSE                      # Apache-2.0
├── package.json                 # npm 元数据 + pi manifest
├── README.md                    # 英文版（默认）
└── README_zh.md                 # 简体中文版
```

`.gitignore` 保留了 `.dev_docs/` 作为本地设计文档的约定目录（不跟踪）；当前工作区里没有这个目录。

## 分支现状

**单分支开发：直接在 `main` 上提交并推送，远端也只有 `main`。**

早期的 `dev` / `test` 分支已于 2026-08-11 删除：两者同为 `5a352b4`，与 `main` **没有共同祖先**（`main` 是后来重新 init 的压缩历史），内容全面落后于 `main`，也从未推送远端 —— merge 它们只会得到一次 unrelated-histories 灾难。删除前已备份为 git bundle：`/tmp/micropowers-dev-branch-backup.bundle`（如需找回：`git bundle unbundle <该文件>`，或 `git branch dev 5a352b4` 在 gc 之前直接恢复）。

`.worktrees/` 目录已空，早期那套 `cd .worktrees/dev/` 的 worktree 开发流程不再适用。

## 发布流程

```bash
# 1. 改动 → 提交（当前直接在 main）
git add -A && git commit -m "<type>: <描述>"

# 2. 升版本（会自动创建 vX.Y.Z tag 并提交 package.json）
npm version patch          # 或 minor / major

# 3. 推送代码 + tag
git push origin main --follow-tags

# 4. 发 npm（必须显式指定官方源，见下方陷阱）
npm publish --registry=https://registry.npmjs.org/
```

**陷阱：本机 `~/.npmrc` 把默认 registry 指向了 `registry.npmmirror.com`（只读镜像）。**
后果是 `npm whoami` / `npm view` 会报 need auth 或返回过期数据 —— 任何需要鉴权或需要准确结果的命令都要加 `--registry=https://registry.npmjs.org/`。`package.json` 的 `publishConfig.registry` 已经把 publish 钉在官方源，但显式加参数更保险。

**发布前自检：**

```bash
npm pack --dry-run          # 确认 files 白名单没漏 skills/ 和 micropowers-styles/
```

`package.json` 的 `files` 是白名单：新增顶层目录时必须同步加进去，否则 npm 包里没有它，而 git 安装方式却有 —— 两种渠道行为不一致是最难查的一类问题。

## 编码规范

### Skill 开发

每个 skill 是 `skills/<name>/SKILL.md`，frontmatter 必须有两个字段：

```markdown
---
name: <skill-name>              # 必须与目录名一致
description: <触发时机导向的一句话，说明「什么时候该用我」>
---
```

`description` 是 agent 唯一的选取依据（它先看到全部 description，再决定读哪个 skill 正文），所以要写触发条件而不是功能罗列。

正文按流程组织，现有 skill 的常用小节：`## The Process`、`## Step N — ...`、`## Style Adaptation`、`## Red Flags`。不强求统一模板，但同一件事在不同 skill 里的写法要一致。

### 提交信息格式

```
<type>: <简短描述>

<可选详细说明>
```

类型：`feat` / `fix` / `docs` / `refactor` / `style` / `chore`

## 质量要求

1. **Plan 先行** — 复杂变更前先写 plan，经过审视再动手
2. **自检闭环** — 修改后 review 自己的变更，确保无遗漏
3. **改 skill 必须验证加载** — 动了 `skills/` 或 `package.json` 后，确认 pi 真的能加载：
   ```bash
   cd /tmp && pi -e npm:micropowers -p "print the location of skill 'micropowers-plan'"
   ```
   本地未发布的改动改用本地路径包验证：`pi install <repo 绝对路径>`
4. **最小变更** — 只做必要修改，不顺手重构无关代码

## 相关文档

- [README.md](README.md) / [README_zh.md](README_zh.md) — 面向用户的定位与安装
- [INSTALL.md](INSTALL.md) — 安装指南
- [使用示例](examples/simple-feature.md) — 完整流程示例

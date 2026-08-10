# Micropowers

> [English](README.md) | **简体中文**

轻量、不碍手碍脚的开发工作流。保留「先对齐、再动手」的纪律，砍掉 ceremony，给模型智力主导权但画清边界。

## 定位

| | Superpowers | Micropowers |
|---|---|---|
| 为谁设计 | 陌生 subagent + 复杂跨模块变更 | 日常需求 + 一个开发者 + 一个 agent |
| 对模型的态度 | 防模型（HARD-GATE、红旗表） | 信模型（智力合伙人） |
| 质量来源 | 下游审查（两人审） | 上游精度（精确 plan）+ 模型判断力 |
| 流程刚性 | 不可跳过 | 默认硬边界，用户可覆盖 |

不是 Superpowers 的简化版。哲学继承，代码独立。不兼容、不依赖、不互操作。

## 流程

```
用户 /micropowers → 入口路由 + 风格选择
                → micropowers-brainstorm（Socratic 对齐）
                → micropowers-plan（精确规格 + 验收清单）
                → micropowers-execute（并行 subagent + TDD 纪律）
                → micropowers-finish（收尾）
```

四个阶段不可跳过——用户打 `/micropowers` 就是签了这份契约；唯一例外是续档：saved plan 直接从执行阶段恢复。极简任务的设计呈现可压缩到一句话，但流程不可跳过。

## 四种协作风格

入口选择一种，全程贯彻。过程中随时可换。

| 风格 | 一句话 | 对齐阶段 | Plan 出稿 | 执行阶段 |
|---|---|---|---|---|
| **快速** | 要结果不要过程 | 最多约3问，直给推荐 | 给结论 | 只报成败 |
| **标准**（默认） | 平衡 | 最多约8问，多选优先 | Task 摘要 | 每 task 一行 |
| **可解释型** | 想理解思路 | 附取舍逻辑 | +设计理由 | +根因分析 |
| **可审计型** | 决策留痕 | 追问到底 + 决策编号 | +决策链接 | 逐条 Verify |

风格文件独立（`skills/micropowers/micropowers-styles/`），不污染核心流程 skill。入口只按需读取所选风格，不一次性加载全部。Plan 内部执行规格四种风格完全相同，风格只影响出稿后给用户呈现什么。

## 模型权限

| | 纯技术决策 | 结构/安全风险 |
|---|---|---|
| **权限** | 自主决定 | 必须站出来，附推荐方案 |
| **条件** | 与用户需求不冲突 | 破坏/臃肿/危险，或有公认行业最佳解法 |

**用户定义 what 和边界，模型负责 how 并守门。** 步骤内模型有智力主导权（提问、方案设计、任务拆分），但不能改变用户意图。

## 人类局限适配

- **认知带宽有限** — 一次一个问题，plan 展示不灌满屏
- **知识储备有限** — 不拷问用户技术细节，自己去项目里读
- **表达不稳定** — 从粗糙描述推断意图，确认关键决策
- **决策疲劳** — 分叉点给强推荐，不灌选项，不硬凑第三选项

## 安装

**推荐：`npx skills` 一键安装整套（5 个 skill 是一个整体，建议全部安装）**

```bash
npx skills add GDWhisper/micropowers -s '*'
```

> `-s '*'` 跳过 skill 勾选，一次安装全部 5 个 skill（micropowers / micropowers-brainstorm / micropowers-plan / micropowers-execute / micropowers-finish）。不加 `-s` 会进入交互勾选界面，需要逐个空格选择。
> **安装到哪个 agent 保留交互选择**（`-a <agent>` 可预设，支持的 agent 见 `npx skills add --help`）；默认装到当前项目，加 `-g` 装到全局。

**告诉你的 agent：**

> Fetch and follow instructions from https://raw.githubusercontent.com/GDWhisper/micropowers/refs/heads/main/INSTALL.md

或手动：

```bash
cp -r micropowers/skills/* ~/.agents/skills/
```

Pi 包安装：

```bash
pi install npm:micropowers
```

> 也可以钉住 git tag：`pi install git:github.com/GDWhisper/micropowers@v0.1.0`。加 `-l` 写入当前项目的 `.pi/settings.json`，默认写入用户级 settings。
> **两种安装方式只能选一个。** pi 里同名 skill 先到先得，且自动发现目录优先于包 —— 所以 `~/.agents/skills/micropowers*` 或 `~/.pi/agent/skills/micropowers*` 下的残留副本（例如 `npx skills` 装的）会静默屏蔽包里的 skill。切换到 pi 包之前先把这些副本删掉。

## 与 Superpowers 的关系

两个项目可以同时安装，互不影响。

## 许可证

Apache License 2.0

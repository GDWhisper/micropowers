# Example: Adding a Filter Button

A simple feature that shows micropowers' full flow.

## Step 1 — User says

> /micropowers 帮我在搜索框旁边加一个过滤按钮

## Step 2 — micropowers entry

Micropowers assesses: this is "Normal" (not trivial, not complex).  Routes to micropowers-brainstorm.

## Step 3 — micropowers-brainstorm

Agent explores project context — reads current search component, checks conventions.

**Agent (Q1):** "过滤按钮是用来筛选已显示的结果，还是触发新的搜索？"

**User:** "筛选已经显示的结果。按日期和类型过滤。"

**Agent (Q2):** "过滤状态需要持久化到 URL 参数里吗，还是只在当前页面状态下工作？"

**User:** "只在当前页面，不需要 URL。"

Agent has enough context.  Proposes:

> **方案（推荐）:** 在 SearchBar 组件旁增加一个 FilterButton。点击弹出 Dropdown，里面有日期范围和类型两个筛选项。FilterButton 显示已选中的筛选数量。筛选逻辑在父组件用 useMemo 处理。
>
> **备选:** 在搜索结果上方加一行 FilterBar。更直观但需要额外的布局空间。
>
> 推荐主方案——耦合度低，SearchBar 不需要知道 FilterButton 内部逻辑。

**User:** "好，用推荐方案。"

Agent presents the design concisely in chat.  User approves.  Agent invokes micropowers-plan.

## Step 4 — micropowers-plan

Agent generates the plan.  Context is only 4 turns — short, good.  Task touches 3 files — borderline.

Agent presents:

> 📋 Plan ready. Two options:
>
> A. Save to disk
>    → 涉及 3 个文件，复杂度适中。定档后新开 session 执行更干净。
>    → Plan saved to: .dev_docs/micropowers/plans/2026-06-30-filter-button.md
>    → Resume: open a new session, type /micropowers
>
> B. Continue here (推荐)
>    → 只用了 4 轮对齐，上下文充裕，直接执行即可。
>
> 选 A 还是 B？

User picks B.

## Step 5 — micropowers-execute

Agent dispatches subagents:

```
Task 1: FilterButton component → subagent creates component with tests
Task 2: Filter logic hook     → subagent adds useFilters hook
Task 3: Wire into SearchBar   → subagent integrates, runs full test suite
```

Each task passes.  Agent runs final tests, presents diff, invokes micropowers-finish.

## Step 6 — micropowers-finish

Agent shows: 3 files changed, 1 new, tests pass.  Recommends commit.  Done.

---

**Total overhead:** 4 Socratic questions, 1 design presentation, 1 plan option, 3 subagent dispatches.  All in one session.  Micropowers adds structure without drowning the feature in process.

---
name: micropowers-plan
description: Use after micropowers-brainstorm confirms the design — generate an implementation plan with the option to archive for later or continue immediately.
---

# Make A Plan

Turn the aligned design from brainstorming into a task list.  Before rushing to execution, let the user decide: archive the plan for later, or continue right now.

**Announce:** Announce what you're doing in the user's language (e.g. "用 micropowers-plan 把设计转成实施计划").

## Step 1 — Generate The Plan

From the brainstorming conversation, extract:

- **Goal**: one sentence
- **Tech stack**: language, framework, key libraries
- **Files affected**: create new / modify existing
- **Tasks**: ordered list of work items

### Task Format

Each task has three fields.  The precision of Change and Verify is the
primary quality gate — a subagent with only this task spec should produce
code indistinguishable from one written with full context.

```
### Task N: [Name]

**Files:** `create path/to/new.py` or `modify path/to/existing.py`

**Change:** [What to build and how it fits.  Include:
- Behavior boundaries: what it does AND does not do
- Interface: props/params/signatures, types, return values
- States: loading, empty, error, edge cases — if relevant
No code.  A skilled engineer should be able to implement from this alone.]

**Verify:** [Acceptance checklist — 3 to 7 concrete, falsifiable items.
Each item describes observable behavior: render, click, call, return, etc.
NOT "code looks good" or "works correctly."  Target: a reviewer who has
never seen this feature should be able to check each item in under a minute.]
```

#### Change field — precision rules

- Name the component / function / module this task produces
- List its public interface (props, params, return type)
- Describe every state the user can see (loading, empty, error, active)
- State what this task does NOT cover ("does not handle persistence" / "styling is out of scope")

#### Verify field — checklist rules

- Each item starts with an observable verb (renders, displays, calls, returns, logs)
- Each item is independently checkable (don't chain "and" across unrelated behaviors)
- Include at least one regression check: "Existing X tests pass" or "Existing Y behavior unchanged"
- Include at least one edge-case check (empty input, boundary value, error path)
- Where the brainstorm design declared performance or security constraints, carry each into a Verify item so it stays checkable

### Task Granularity

A task should take 10–20 minutes for a competent developer.  Not 2 minutes (too granular) and not 2 hours (too risky for a subagent).  Split only where two tasks could reasonably pass or fail independently.

### Plan Template

```markdown
# [Feature Name] — Implementation Plan

**Goal:** [one sentence]

**Tech Stack:** [language, framework, key deps]

**Plan saved via:** micropowers/micropowers-plan

---

### Task 1: [Name]
**Files:** ...
**Change:** ...
**Verify:** ...

### Task 2: [Name]
...
```

## Step 2 — Plan Self-Review

Before presenting the plan, read every Verify checklist and confirm each
item is independently checkable.  If any item is vague ("works correctly",
"looks good", "handles edge cases"), rewrite it.  If any task is missing
a regression check or edge-case check, add it.

This is a 30-second pass, not a formal audit.  Fix issues inline.

## Style Adaptation (presentation only)

The plan body (Goal, Tech Stack, Files, Task Change/Verify fields) is **for the subagent** and does NOT change by style. Style only affects what you show the user after plan generation.

After Step 2 (self-review), present the plan according to the loaded style overlay:

| Style | Presentation |
|---|---|
| Fast | 「N tasks, M files, [有/无]依赖。A 保存 B 执行？」 |
| Standard | Task list summary (each task one-liner: name + files) + A/B choice |
| Explainable | Standard + per task: design rationale, rejected alternatives |
| Auditable | Standard + per task: decision point cross-references (→ D1, D3) |

**Presentation always includes the A/B choice** (save to disk vs. continue here), regardless of style.

If no style has been loaded, default to standard.

## Step 3 — Assess and Present Options

Before presenting the plan, assess whether it should be archived or executed inline.

### Decision Signals

| Signal | Suggests |
|---|---|
| Conversation > 15 turns, or approaching context limit | **Archive** — model quality degrades with long context |
| Task count ≥ 5 or files affected ≥ 3 | **Archive** — complexity benefits from a fresh session |
| User said "later" / "下次" / "回头" / "改天" | **Archive** — explicit intent to defer |
| User said "go ahead" / "直接开始" / "继续" | **Continue** — explicit intent to proceed |
| Conversation < 8 turns + only 1–2 files affected | **Continue** — context is fresh, task is simple |
| No strong signal either way | **Archive (偏保守)** — default to the safe option |

Always give both options, but mark one **(推荐)** based on the signals above.  Include a one-line reason.

### Presentation Format

Present in the user's language:

```
📋 Plan ready. 两个选择：

A. 保存到磁盘 (推荐)
   → [一句话理由，如 "10 轮对齐对话，上下文较长，新开 session 执行更稳"]
   → 保存到: .dev_docs/micropowers/plans/YYYY-MM-DD-<feature>.md
   → 续档: 新开会话，输入 /micropowers 或 /micropowers <路径>

B. 本会话直接执行
   → 用 subagent 集群作业（micropowers-execute）并行执行所有 task
   → Plan 不落盘，保留在对话中

选 A 还是 B？（选 A 下次续档也会走到 micropowers-execute 执行）
```

## Step 4 — Route Based on Choice

### If A (Save to disk)

1. Write the plan to `.dev_docs/micropowers/plans/YYYY-MM-DD-<feature>.md`
2. Create the directory if it doesn't exist
3. Tell the user (in their language): "Plan 已保存到 .dev_docs/micropowers/plans/<file>"
4. **Ask the user:**

```
Plan 已保存。要现在用 micropowers-execute 集群作业执行，还是下次？

A. 现在执行 → 立即在本会话中派 subagent 并行作业
B. 下次 → 新开会话，输入 /micropowers 或 /micropowers .dev_docs/micropowers/plans/<file>
```

5. If A → invoke `micropowers-execute` to execute
6. If B → output the resume command and stop.  Do NOT wait for a reply — the user may switch sessions immediately.

### If B (Continue)

1. Do NOT write the plan to disk — it lives in the conversation
2. Invoke `micropowers-execute` to execute (subagent 集群并行作业).  The choice of B IS the confirmation — do NOT ask "ready?" / "确认开始执行？" afterwards.

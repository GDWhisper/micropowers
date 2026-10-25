---
name: micropowers
description: Use when the user invokes /micropowers or starts a lightweight development task — the align-first workflow entry. Resumes saved plans, selects a collaboration style, then routes into Socratic alignment. This is the ONLY entry point; the four workflow phases are internal files read from this skill directory, never separate skills.
---

<HARD-GATE>
When `/micropowers` is invoked, you MUST execute the routing logic immediately.

Do NOT ask the user "what do you want to do?" or "should we continue?" before routing.  Do NOT pause for clarification.  Do NOT gather more context first.

The user invoked `/micropowers`.  They already chose the workflow.  Your job is to run the routing and announce where you're going.

**Routing → Announce → Read the phase file.  That is the only valid sequence.**

All announcements MUST use the user's language.  If the user speaks Chinese, all prompts, options, and status lines are Chinese.
</HARD-GATE>

# Micropowers

Micropowers is a lightweight, self-contained development workflow.  It keeps the discipline of "align first, code later" without drowning simple features in process.

## One Entry, Four Phases

`/micropowers` is the only command.  The workflow has four phases, and they live as **files inside this skill's directory**:

| Phase | File | What it does |
|---|---|---|
| 1 | `phases/brainstorm.md` | Socratic alignment — questions, approaches, design approval |
| 2 | `phases/plan.md` | precise task list + acceptance checklists |
| 3 | `phases/execute.md` | parallel subagents + TDD discipline |
| 4 | `phases/finish.md` | wrap-up + next-step recommendation |

**These are not skills.**  There is no `/micropowers-plan`, no `/micropowers-execute`, no `/micropowers-brainstorm`, no `/micropowers-finish`.  When a phase hands off, it tells you to **read the next phase file**, and you continue as that phase.

### How to resolve a phase file path

Every path in this skill and its phase files is **relative to this SKILL.md's own directory**.  Resolve it in this order — stop at the first one that works:

1. **Platform variable** — if your platform substitutes a skill-directory variable (CodeBuddy: `${CODEBUDDY_SKILL_DIR}`, Claude Code: `${CLAUDE_SKILL_DIR}`), use it.  A platform that does not support these leaves the text as-is — in that case treat the literal text as "the directory containing this SKILL.md".
2. **Skill list location** — if your skill list shows this skill's SKILL.md path, take its directory.
3. **Glob fallback** — if neither is available, locate the file with Glob `**/micropowers/phases/<name>.md`.

Then **read the file and follow it.**  A phase file's instructions take over from that point on — you are no longer following this entry file.

Same rule for `styles/<name>.md` (collaboration styles) and `companion/visual-companion.md` (optional browser companion).

---

## Routing

When the user says `/micropowers` (or asks to build/add/create something after `/micropowers` was loaded), run this immediately:

```
User invoked /micropowers [optional: plan file path]
    │
    ├── Path given? → Announce "Resuming plan at <path>" → read phases/execute.md → read phases/finish.md
    │
    └── No path
         │
         ▼
    Check: saved plans in .dev_docs/micropowers/plans/ ?
         │
         ├── YES → Announce "Found N saved plan(s)" → ask which one → read phases/execute.md → read phases/finish.md
         │
         └── NO  → Pick collaboration style → Announce "Starting brainstorm" → read phases/brainstorm.md
```

**Critical rule:** after the routing decision is made, announce it in one line and read the phase file immediately.  Do NOT ask the user to confirm the routing.  Do NOT summarize the plan.  Do NOT solicit feedback on the routing decision.  The user will provide feedback on the output of the next phase.

## Step 1 — Saved Plan Check

**If user provided a path** (`/micropowers .dev_docs/micropowers/plans/2026-06-30-filter.md`):
1. Verify the file exists and is a micropowers plan (check for `Plan saved via: micropowers/` marker)
2. If found: announce "Resuming plan: <filename> — <goal>" → read `phases/execute.md` NOW.  Do NOT ask "Continue?" — the user gave you the path.
3. **If file not found:** "No plan at that path." Then scan `.dev_docs/micropowers/plans/` for saved plans — same as the no-path flow below.

**If no path given:**
1. Scan `.dev_docs/micropowers/plans/` for saved plans
2. If exactly one plan found: display and ask "Continue?"
3. If multiple plans found: list them all (filename + goal), ask "Continue from one?"
4. If no plans found: proceed to Step 2
5. User declines all → proceed to Step 2

## Step 2 — Style Selection

Before entering the brainstorm phase, present the four collaboration styles and let the user choose.

The prompt MUST be exactly this (or its Chinese equivalent — match the user's language):

```
开始之前，选一下这次的协作风格（过程中随时可以换，说「切风格名」就行）：

1. **快速** — 要结果不要过程，技术细节你定，只在方向性大问题上找我
2. **标准** — 关键决策问我，技术细节你自动处理（默认）
3. **可解释型** — 我想理解你的思路、决策逻辑和排掉的方案
4. **可审计型** — 每一步决策留痕，方便追溯和团队审查

选哪个？直接说 1234。
```

**Rules:**
- The prompt above is the ONLY content of this message. Do NOT add framing, explanation, or other content.
- Default to 2 (standard) if the user says "随便" / "都行" / "你定" / "默认" or doesn't answer.
- If the user says "先开始" / "直接来" / "继续", default to standard.
- After the user selects, immediately load ONLY the style file for the chosen `<name>`: `styles/<name>.md`, resolved **relative to this skill file's own directory** (same resolution rules as the phase files above). Read it and keep it in context as the active style.
- Fallback only if that path does not exist: locate it via Glob `**/micropowers/styles/<name>.md`.
- Read ONLY the selected `<name>.md`. Do NOT read or load the other three style files — they stay dormant until the user switches styles (say 「切风格名」).
- If the style file cannot be located, announce that, default to standard, and continue — do not stall the routing.
- Announce the style in ONE line: "风格：<name> — 开始 brainstorm"
- Then read `phases/brainstorm.md` NOW and continue as Phase 1.

**Style file mapping:**

| User selection | File |
|---|---|
| 1 / "快速" / "fast" | `styles/fast.md` |
| 2 / "标准" / "standard" | `styles/standard.md` |
| 3 / "可解释" / "explainable" | `styles/explainable.md` |
| 4 / "可审计" / "auditable" | `styles/auditable.md` |

## Session Resumption

Resume a saved plan in two ways:

**Direct path:** `/micropowers .dev_docs/micropowers/plans/2026-06-30-filter.md`
→ Opens the plan immediately.  "Continue?" → read `phases/execute.md` → read `phases/finish.md`.

**Scan:** `/micropowers`
→ Lists all saved plans in `.dev_docs/micropowers/plans/`.  Pick one or start fresh.

## Red Flags

These thoughts mean STOP — you're rationalizing.  Act, don't ask.

| Thought | Reality |
|---------|---------|
| "Let me ask what they want to do first" | They invoked /micropowers.  They already chose.  Route. |
| "Let me gather more context before routing" | Routing IS the first step.  The brainstorm phase handles context. |
| "This might need more discussion first" | The workflow starts NOW.  Do not pre-discuss. |
| "Let me explain the routing to them" | Announce in one line, then read the phase file.  No summaries. |
| "This looks simple, let me just do it" | If they wanted you to just do it, they wouldn't have typed /micropowers. |
| "I'll plan as I go" | Unplanned work produces unplanned bugs.  Follow the workflow. |
| "Should I invoke the micropowers-plan skill?" | There is no such skill.  Read `phases/plan.md`. |

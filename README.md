# Micropowers

> **English** | [简体中文](README_zh.md)

A lightweight, no-friction development workflow. Keeps the discipline of "align first, code later" while cutting the ceremony — giving the model intellectual initiative but drawing clear boundaries.

## Positioning

| | Superpowers | Micropowers |
|---|---|---|
| Designed for | Unfamiliar subagents + complex cross-module changes | Everyday requests + one developer + one agent |
| Attitude toward the model | Guard against the model (HARD-GATE, red flags) | Trust the model (intellectual partner) |
| Source of quality | Downstream review (two-person review) | Upstream precision (precise plan) + model judgment |
| Process rigidity | Never skippable | Hard boundaries by default, user can override |

Not a simplified Superpowers. Philosophy inherited, code independent. No compatibility, no dependency, no interop.

## Flow

```
User /micropowers → entry routing + style selection
                 → micropowers-brainstorm (Socratic alignment)
                 → micropowers-plan (precise spec + acceptance checklist)
                 → micropowers-execute (parallel subagents + TDD discipline)
                 → micropowers-finish (wrap-up)
```

The four phases cannot be skipped — typing `/micropowers` signs that contract; the only exception is resuming: a saved plan resumes directly into the execution phase. The design presentation for a trivial task can be compressed to one sentence, but the flow itself is not skippable.

## Four Collaboration Styles

Pick one at the entry; it applies throughout. Switch at any time.

| Style | In one line | Alignment phase | Plan output | Execution phase |
|---|---|---|---|---|
| **Fast** | Results over process | at most ~3 questions, give recommendation | Give the conclusion | Report pass/fail only |
| **Standard** (default) | Balanced | at most ~8 questions, multiple-choice preferred | Task summary | One line per task |
| **Explainable** | Wants to understand the thinking | Attach trade-off logic | + design rationale | + root-cause analysis |
| **Auditable** | Every decision leaves a trace | Drill down + decision IDs | + decision links | Verify each item |

Style files live separately (`skills/micropowers/micropowers-styles/`) so they don't pollute the core workflow skills. The entry loads only the selected style on demand — never all four at once. The plan's internal execution spec is identical across all four styles; the style only affects what the user sees after the plan is produced.

## Model Authority

| | Pure technical decisions | Structural / security risks |
|---|---|---|
| **Authority** | Decide autonomously | Must speak up, with a recommended option |
| **Condition** | Does not conflict with the user's request | Destructive / bloated / dangerous, or a well-known industry best practice exists |

**The user defines the what and the boundaries; the model owns the how and keeps the gate.** Within a step, the model has intellectual initiative (asking questions, designing solutions, splitting tasks), but cannot change the user's intent.

## Human Limitations

- **Limited cognitive bandwidth** — one question at a time, don't flood the screen with the plan
- **Limited knowledge** — don't grill the user on technical details, read the project yourself
- **Unstable expression** — infer intent from rough descriptions, confirm key decisions
- **Decision fatigue** — give a strong recommendation at forks, don't flood with options, don't force a third option

## Installation

**Recommended: `npx skills` — install the whole set with one command (the 5 skills are one unit; install all of them)**

```bash
npx skills add GDWhisper/micropowers -s '*'
```

> `-s '*'` skips the per-skill checklist and installs all 5 skills at once (micropowers / micropowers-brainstorm / micropowers-plan / micropowers-execute / micropowers-finish). Without `-s`, you'll get an interactive checklist where you select skills one by one with the spacebar.
> **Which agent to install into is left to the interactive prompt** (`-a <agent>` can preset it; supported agents are listed in `npx skills add --help`). Installs into the current project by default; add `-g` for a global (user-level) install.

**Tell your agent:**

> Fetch and follow instructions from https://raw.githubusercontent.com/GDWhisper/micropowers/refs/heads/main/INSTALL.md

Or manually:

```bash
cp -r micropowers/skills/* ~/.agents/skills/
```

Pi package:

```bash
pi install git:github.com/GDWhisper/micropowers
```

## Relationship to Superpowers

Both projects can be installed side by side without affecting each other.

## License

Apache License 2.0

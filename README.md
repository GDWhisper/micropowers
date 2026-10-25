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
                 → Phase 1 Brainstorm (Socratic alignment)
                 → Phase 2 Plan (precise spec + acceptance checklist)
                 → Phase 3 Execute (parallel subagents + TDD discipline)
                 → Phase 4 Finish (wrap-up)
```

The four phases cannot be skipped — typing `/micropowers` signs that contract; the only exception is resuming: a saved plan resumes directly into the execution phase. The design presentation for a trivial task can be compressed to one sentence, but the flow itself is not skippable.

**One entry point.** The four phases are plain files under `skills/micropowers/phases/`, not separate skills — the user sees a single `/micropowers`, and the AI's skill index holds a single entry, so nothing but the entry competes for attention.

## Four Collaboration Styles

Pick one at the entry; it applies throughout. Switch at any time.

| Style | In one line | Alignment phase | Plan output | Execution phase |
|---|---|---|---|---|
| **Fast** | Results over process | at most ~3 questions, give recommendation | Give the conclusion | Report pass/fail only |
| **Standard** (default) | Balanced | at most ~8 questions, multiple-choice preferred | Task summary | One line per task |
| **Explainable** | Wants to understand the thinking | Attach trade-off logic | + design rationale | + root-cause analysis |
| **Auditable** | Every decision leaves a trace | Drill down + decision IDs | + decision links | Verify each item |

Style files live separately (`skills/micropowers/styles/`) so they don't pollute the core workflow. The entry loads only the selected style on demand — never all four at once. The plan's internal execution spec is identical across all four styles; the style only affects what the user sees after the plan is produced.

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

**Recommended: `npx skills` — install with one command**

```bash
npx skills add GDWhisper/micropowers -s '*'
```

> `-s '*'` skips the per-skill checklist and installs everything (now a single `micropowers` skill). Without `-s`, you'll get an interactive checklist.
> **Which agent to install into is left to the interactive prompt** (`-a <agent>` can preset it; supported agents are listed in `npx skills add --help`). Installs into the current project by default; add `-g` for a global (user-level) install.

**Tell your agent:**

> Fetch and follow instructions from https://raw.githubusercontent.com/GDWhisper/micropowers/refs/heads/main/INSTALL.md

Or manually:

```bash
cp -r micropowers/skills/micropowers ~/.agents/skills/
```

> **Upgrading from an older version**: delete the old sibling copies (`micropowers-brainstorm` / `micropowers-plan` / `micropowers-execute` / `micropowers-finish`) first — otherwise they keep loading as standalone skills. See [INSTALL.md](INSTALL.md).

Pi package:

```bash
pi install npm:micropowers
```

> Or pin a git ref instead: `pi install git:github.com/GDWhisper/micropowers@v0.1.0`. Add `-l` to write to the current project's `.pi/settings.json` rather than your user settings.
> **Pick one install method, not both.** Pi resolves skill-name collisions first-come-first-served, and its auto-discovered directories win over packages — so leftover copies in `~/.agents/skills/micropowers*` or `~/.pi/agent/skills/micropowers*` (e.g. from `npx skills`) silently shadow the package's skills. Delete those copies before switching to the pi package.

## Relationship to Superpowers

Both projects can be installed side by side without affecting each other.

## License

Apache License 2.0

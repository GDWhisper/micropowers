<!--
Phase file — NOT a skill.
Resolved relative to the micropowers skill directory: micropowers/phases/finish.md
Invoked by Phase 3 (execute) only.
-->

# Phase 4 — Finish

Wrap up a completed feature branch.

## The Process

1. **Verify tests pass**
   ```bash
   # Run the project's test suite — use whatever test runner the project uses
   ```

2. **Summarize the diff**
   - Files changed / added / deleted
   - One-line summary of what each change does

3. **Recommend next step**
   Present options:
   - Commit and merge / open PR
   - Keep branch for later
   - Discard

4. **Execute user's choice**

## Style Adaptation

Summary format varies by loaded style. If no style has been loaded, default to standard.

| Style | Summary format |
|---|---|
| Fast | 「M 文件改完，测试通过。提交？」 |
| Standard | Files changed/added/deleted + one-line what each does |
| Explainable | Standard + key implementation decisions made |
| Auditable | Standard + complete change log with decision point references |

## When Called From Phase 3

Phase 3 (execute) runs Phase 4 automatically after all tasks complete.  When this file is entered from Phase 3, steps 1-2 are already done — skip to step 3.

## When Resumed From a Saved Plan

User starts a new session, types `/micropowers`, and the entry skill detects a saved plan.  After Phase 3 replays the remaining tasks, Phase 4 is entered to wrap up.

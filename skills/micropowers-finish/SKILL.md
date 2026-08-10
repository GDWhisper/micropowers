---
name: micropowers-finish
description: Use after micropowers-execute completes, or when resuming from a saved plan — wrap up the branch and decide next step.
---

# Finishing

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

## When Called From micropowers-execute

`micropowers-execute` runs micropowers-finish automatically after all tasks complete.  When micropowers-finish is invoked by micropowers-execute, steps 1-2 are already done — skip to step 3.

## When Called Standalone

User starts a new session, types `/micropowers`, and micropowers detects a saved plan.  After micropowers-execute replays the remaining tasks, micropowers-finish is called to wrap up.

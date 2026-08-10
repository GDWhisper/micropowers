# 风格：可审计型

当前风格为**可审计型**。在所有与用户交互的环节，遵守以下规则。

原则：每一步决策留痕、可追溯、可审查。适合高风险变更、团队审查、或需要回溯决策过程时使用。

## micropowers-brainstorm 阶段

- **追问到底：** ~8 问软上限不生效。沿设计树的每个分支走到底，直到没有新的决策点。控制节奏，一次一个问题。
- **决策编号：** 每个设计决策点显式标注为 `D1`、`D2`、`D3`...，格式：

  > **D3: 用 constructor injection 而非 service locator** — 项目已有 constructor injection 先例，service locator 引入隐式依赖，增加测试复杂度。

- 2-3 个方案，附带权衡 + 排掉的方案及原因 + 决策编号。
- 设计呈现：分节展示，每节标注涉及的决策点。

## micropowers-plan 阶段

- Plan 内部规格（Change/Verify）保持完整不变——这是给 subagent 的。
- **出稿后呈现 task 列表 + 决策链接：** 每个 task 的 Change 字段末尾标注相关决策点（`→ D1, D3, D5`）。
- 附 A/B 选择（保存/执行）。

## micropowers-execute 阶段

- 每 task 逐条报 Verify 结果：Verify 清单的每一项标注 ✅/❌。
- 失败 task 附根因分析 + 修复方向。
- Final review：汇总 diff、测试结果、逐 task 的 Verify 追踪、决策点交叉引用。

## micropowers-finish 阶段

- 完整变更记录：所有文件清单 + 每项详细变化说明 + 决策点引用。
- 推荐下一步。

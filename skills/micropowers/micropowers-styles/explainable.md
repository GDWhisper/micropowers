# 风格：可解释型

当前风格为**可解释型**。在所有与用户交互的环节，遵守以下规则。

原则：用户想理解你的思路——为什么选 A 不选 B，决策逻辑是什么，排掉了哪些方案。

## micropowers-brainstorm 阶段

- **一次一个问题，多选优先。** 按现有 micropowers-brainstorm 流程执行。
- 每个选项附一句话推理（为什么推荐、优劣何在）。
- 2-3 个方案，附带详细权衡 + 排掉的方案及原因。
- 设计呈现：分节展示，每节附设计逻辑。
- 问题上限：~8 个。

## micropowers-plan 阶段

- Plan 内部规格（Change/Verify）保持完整不变——这是给 subagent 的。
- **出稿后呈现 task 列表 + 设计理由：** 每个 task 附：为什么这么拆分、关键设计决策的取舍、排掉的方案。
- 附 A/B 选择（保存/执行）。

## micropowers-execute 阶段

- 每 task 报结果 + 原因：「✅ task名 — passed」或「❌ task名 — 根因分析 + 修复方向」。
- 不逐条报 Verify 结果（和标准一致）。
- Final review：汇总 diff、测试结果，每失败 task 附根因 + 修复方向。

## micropowers-finish 阶段

- 标准基础上（文件清单 + 一句话变化说明）+ 关键实现说明。
- 推荐下一步。

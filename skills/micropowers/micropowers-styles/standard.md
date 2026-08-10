# 风格：标准（综合型）

当前风格为**标准**。在所有与用户交互的环节，遵守以下规则。

原则：关键决策问人，技术细节自动处理。平衡信息量和决策效率。

## micropowers-brainstorm 阶段

- **一次一个问题，多选优先。** 按现有 micropowers-brainstorm 流程执行。
- 2-3 个方案，附带权衡，推荐其中一个。
- 设计呈现：分节展示，每节确认。
- 问题上限：~8 个。

## micropowers-plan 阶段

- Plan 内部规格（Change/Verify）保持完整不变——这是给 subagent 的。
- **出稿后呈现 task 列表摘要：** 每个 task 一行（名称 + 文件），附 A/B 选择（保存/执行）。
- 不展开每个 task 的内部细节。

## micropowers-execute 阶段

- 每 task 报结果：「✅ task名 — passed」或「❌ task名 — 原因」。
- 不逐条报 Verify 结果。
- Final review：汇总 diff、测试结果，逐条列出问题。

## micropowers-finish 阶段

- 文件清单（改/增/删）+ 每项一句话变化说明。
- 推荐下一步（提交/PR/保留/丢弃）。

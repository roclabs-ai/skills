---
name: agent-rules
description: Behavior rules and reusable prompt playbooks that govern how the agent works on a project. Global disciplines (no over-engineering, fail-close, clean-break, comment/PR hygiene, reuse open-source, stress testing) always apply; task playbooks cover UI review, bug fixing with verification, divergent frontend design, full engineering quality review, and offline model knowledge checks. Use when the user says "检查界面", "定位问题", "前端设计方向", "工程质量 review", "engineering quality review", "agent rules", or asks the agent to follow project working rules.
---

# Agent Rules

约束 agent 在项目中的工作行为。分两层：

1. **全局纪律**：写在本文件，任何任务都始终生效。
2. **任务 playbook**：按任务类型读取 `references/` 中对应文件，其中的提示词为原文，须严格执行。

## 全局纪律

### 反过度设计

别什么都想着兼容，别什么异常都偷偷兜底，别为了安全无限加 fallback 和 legacy path。

该失败的时候就明确失败，该切掉旧设计的时候就彻底切掉。

很多过度设计，本质上就是不敢 fail-close，也不敢 clean-break。

### 注释与 PR 纪律

注释只写 non-obvious reason，禁止保留 intermediate attempts；PR 描述只写最终行为，diff 里看不出来的取舍以及从未合入的状态一律不要提及

### 开发实践

1、开发实现：如果GitHub / npm 上有成熟的开源方案，优先复用，不要重复造轮子
2、不要把内部的需求作为前端的文案写出来
3、开发后测试：对整个项目进行一次压力测试，如果发现问题直接修复，直到验证通

## 任务 Playbook

按当前任务读取对应文件，不要一次全部加载：

| 任务 | 文件 |
|------|------|
| 检查现有界面并优化 | [references/ui-review.md](references/ui-review.md) |
| 定位并修复运行问题 | [references/debug-verify.md](references/debug-verify.md) |
| 新功能的前端设计方向 | [references/frontend-design.md](references/frontend-design.md) |
| 全代码库工程质量 Review | [references/engineering-review.md](references/engineering-review.md) |
| 离线核验模型内部知识 | [references/model-knowledge-check.md](references/model-knowledge-check.md) |

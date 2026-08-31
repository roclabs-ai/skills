---
name: agents
description: Behavior rules and reusable prompt playbooks that govern how the agent works on a project. Global disciplines (no over-engineering, fail-close, clean-break, comment/PR hygiene, reuse open-source, stress testing) always apply; task playbooks cover UI review, bug fixing with verification, divergent frontend design, full engineering quality review, offline model knowledge checks, critical non-sycophantic responses, and plain-language answers. Use when the user says "检查界面", "定位问题", "前端设计方向", "工程质量 review", "engineering quality review", "不要附和我", "说人话", "agents", or asks the agent to follow project working rules.
---

# Agents

Constrains how the agent works on a project. Two layers:

1. **Global disciplines**: written in this file, always in effect for every task.
2. **Task playbooks**: load the matching file under `references/` by task type; the prompts inside are verbatim and must be executed exactly as written.

## Global Disciplines

### No Over-Engineering

Do not try to stay compatible with everything, do not silently swallow every exception, and do not pile up fallbacks and legacy paths in the name of safety.

Fail explicitly when failure is the right outcome; cut old designs away completely when they should be cut.

Most over-engineering is, at its core, a refusal to fail-close and a refusal to clean-break.

### Comment and PR Hygiene

Comments only explain non-obvious reasons; never keep intermediate attempts. PR descriptions only state the final behavior — trade-offs invisible in the diff and states that were never merged must not be mentioned.

### Development Practices

1. Implementation: if a mature open-source solution exists on GitHub / npm, prefer reusing it; do not reinvent the wheel.
2. Never surface internal requirements as user-facing frontend copy.
3. Post-development testing: stress test the whole project; fix any problem found immediately and repeat until it passes verification.

## Task Playbooks

Load the file matching the current task; do not load them all at once:

| Task | File |
|------|------|
| Review and optimize an existing UI | [references/ui-review.md](references/ui-review.md) |
| Locate and fix a runtime problem | [references/debug-verify.md](references/debug-verify.md) |
| Frontend design directions for a new feature | [references/frontend-design.md](references/frontend-design.md) |
| Full-codebase engineering quality review | [references/engineering-review.md](references/engineering-review.md) |
| Offline model knowledge check (ChatGPT/Codex only, time-sensitive probes) | [references/model-knowledge-check.md](references/model-knowledge-check.md) |
| Critical, non-sycophantic responses | [references/critical-thinking.md](references/critical-thinking.md) |
| Plain-language answers | [references/plain-language.md](references/plain-language.md) |

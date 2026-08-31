---
name: create-plan
description: Create an executable plan as a Markdown checkbox file under the project's plans/ directory (or a user-specified one), self-review the draft once before delivering, and keep checkboxes updated as work completes. Use when the user says "创建计划", "制定计划", "写个计划", "create a plan", "plan file", or asks to track a task plan with checkboxes.
---

# Create Plan

Create plans as checkbox files that live in the project, get reviewed before delivery, and stay in sync with actual progress.

## Workflow

### 1. Determine the target directory

Plans live in the `plans/` directory at the project root by default. If the user specifies a different directory, use that instead. Create the directory if it does not exist.

Default file name: a short kebab-case name describing the plan, e.g. `plans/login-refactor.md`, unless the user names the file. If the file already exists, ask whether to overwrite, append, or create a new file.

### 2. Draft the plan

Write the plan using this template:

```markdown
# <Plan Title>

Goal: <one sentence describing the end state>

## Tasks

- [ ] <Task 1 — concrete action with a verifiable done-condition>
- [ ] <Task 2>
- [ ] <Task 3>
```

Rules for tasks:

- Every item is a concrete action with a clear, verifiable done-condition — "works correctly" is not one; "X test passes" or "page renders Y" is.
- Order items by execution order and dependency.
- One item per deliverable; split anything that cannot be checked off in a single step.

### 3. Self-review (mandatory, one pass)

Before presenting the plan, reread it against the original requirement and check:

- Missing steps: does completing every checkbox actually reach the goal?
- Wrong order: does any item depend on a later item?
- Vague items: does every item have a verifiable done-condition?
- Over-engineering: is any item unnecessary for the goal? Delete it.

Fix what the review finds, then deliver. State that the self-review was done and what it changed (or that it changed nothing).

### 4. Track execution

- When a task is completed **and verified**, immediately flip its `- [ ]` to `- [x]` in the plan file.
- Never check off an item that was skipped, partially done, or unverified — instead note the blocker under the item.
- If the plan changes during execution (new tasks, dropped tasks), edit the file so it always reflects reality.

## Example

User says: "创建一个重构登录模块的计划"

1. Create `plans/login-refactor.md` (no directory given, so use the default `plans/`).
2. Draft goal + ordered checkbox tasks for the refactor.
3. Self-review: found the draft missing a "run existing login tests" item — added it before delivering.
4. During execution, check off each task right after it is verified done.

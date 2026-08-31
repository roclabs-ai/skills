# Skills

A collection of reusable AI agent skills for software engineering workflows.

## Structure

```
skills/
└── <skill-name>/
    ├── SKILL.md                # Skill definition (required)
    └── references/             # Supporting reference docs
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [agents](skills/agents/) | Behavior rules and reusable prompt playbooks: global disciplines plus task playbooks for UI review, verified bug fixing, frontend design directions, engineering quality review, offline model knowledge checks, critical non-sycophantic responses, and plain-language answers |
| [device-autostart](skills/device-autostart/) | Deploy current project to an adb-connected TinaLinux device and configure it as the boot-time autostart app |
| [create-plan](skills/create-plan/) | Create an executable plan as a Markdown checkbox file under plans/, self-review it once, and keep checkboxes in sync with actual progress |
| [eli5](skills/eli5/) | Explain a topic like I'm a 5 year old, as an HTML artifact with big pictures and few words (from Anthropic's [claude-plugins-community](https://github.com/anthropics/claude-plugins-community/blob/main/eli5/skills/eli5/SKILL.md)) |

## Adding a Skill

1. Create a new directory under `skills/`
2. Add a `SKILL.md` with frontmatter (`name`, `description`) and the full skill prompt
3. Add any reference material in `references/`

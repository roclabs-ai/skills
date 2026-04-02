# Skills

A collection of reusable AI agent skills for software engineering workflows.

## Structure

```
skills/
└── <skill-name>/
    ├── SKILL.md                # Skill definition (required)
    ├── agents/                 # Configs for other AI platforms
    │   └── openai.yaml         # Codex / OpenAI agent metadata
    └── references/             # Supporting reference docs
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [device-autostart](skills/device-autostart/) | Deploy current project to an adb-connected TinaLinux device and configure it as the boot-time autostart app |

## Adding a Skill

1. Create a new directory under `skills/`
2. Add a `SKILL.md` with frontmatter (`name`, `description`) and the full skill prompt
3. Optionally add agent configs for other platforms in `agents/`
4. Add any reference material in `references/`

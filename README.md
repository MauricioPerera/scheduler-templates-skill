# Scheduler Templates Skill

Skill for MCP-enabled agents to manage local automation scheduling via the `local-scheduler-mcp` server.

## What it does

Provides any MCP-enabled agent with specialized context on when and how to use the `local-scheduler-mcp` tools. Includes:

- **Built-in templates**: `build-project`, `disk-check`, `git-sync`
- **Decision flow**: template vs. custom automation vs. one-shot task
- **Parameter interpolation**: `${key}` with JSON string
- **Common patterns**: periodic builds, monitors, long-running tasks

## Installation

### Codex Desktop

```bash
cd ~/.codex/skills
git clone https://github.com/MauricioPerera/scheduler-templates-skill.git scheduler-templates
```

### Other MCP-enabled agents

Copy `SKILL.md` into your agent's context or prompt injection pipeline. The file is a plain Markdown instruction set that tells any LLM how and when to use the scheduler MCP tools.

## Available Templates

| ID | Default Action | Default Interval | Use |
|---|---|---|---|
| `build-project` | `dotnet build` | 60 min | Build .NET projects |
| `disk-check` | `Get-PSDrive C \| Select-Object Used,Free` | 5 min | Monitor disk space |
| `git-sync` | `git pull` | 30 min | Keep repo synced |

## Requirements

- [local-scheduler-mcp](https://github.com/MauricioPerera/local-scheduler-mcp) v3.1.1+
- Any agent with MCP stdio transport support

## Structure

```
scheduler-templates/
├── SKILL.md          # LLM context / instructions (agent-agnostic)
└── README.md         # Human documentation
```

## License

MIT

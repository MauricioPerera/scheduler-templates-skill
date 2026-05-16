---
name: scheduler-templates
description: >
  Use when an MCP-enabled agent needs to create, manage, or reason about recurring
  automated tasks (automations) and one-shot background tasks using the
  local-scheduler MCP server. This skill covers: scheduling periodic
  commands/scripts, choosing between templates and custom automations, parameter
  interpolation, and monitoring task results. Trigger when the user says things
  like: "check disk every 5 minutes", "build this project every hour",
  "run this script periodically", "schedule a task", "monitor X automatically",
  or when working with the local-scheduler MCP tools.
---

# Scheduler Templates Skill

## When to Use

- The user wants an action to run automatically on a schedule.
- The user mentions repeating intervals (every N minutes/hours).
- The user asks to "monitor", "check", "build", or "sync" something periodically.
- The agent is deciding between `create_automation` (fully custom) and `instantiate_template` (pre-built recipe).

## Available MCP Tools

| Tool | Purpose |
|---|---|
| `list_templates` | See built-in automation templates: `build-project`, `disk-check`, `git-sync` |
| `instantiate_template` | Create an automation from a template with optional overrides |
| `create_automation` | Fully custom automation (command or inline script) |
| `run_task` | One-shot background task |
| `list_automations` | View all recurring automations and their last log |
| `get_automation_logs` | Get recent run logs for an automation |
| `check_notifications` | See pending execution results |
| `ack_notifications` | Mark notifications as read |
| `get_task_status` / `list_tasks` | Track one-shot tasks |
| `delete_automation` / `delete_task` | Remove an automation or task |

## Built-in Templates

| ID | Default Action | Default Interval | When to Use |
|---|---|---|---|
| `build-project` | `dotnet build` | 60 min | User wants to build a .NET project periodically |
| `disk-check` | `Get-PSDrive C \| Select-Object Used,Free` | 5 min | User wants to monitor disk space |
| `git-sync` | `git pull` | 30 min | User wants to keep a repo synced |

## Decision Flow

1. Does the user want a common task that matches a built-in template?
   → Use `instantiate_template` with the matching `templateId`.
2. Does the user need something custom (specific command, inline script, different language)?
   → Use `create_automation` directly.
3. Does the user want to run something once and check the result later?
   → Use `run_task`.

## Parameter Interpolation

Templates support `${key}` placeholders in `command` or `script`.
To pass values, use the `params` argument as a **JSON string**:

```json
{ "repoPath": "D:/repos/SnakeGame" }
```

Example: `instantiate_template` with `templateId: "build-project"` and `params: '{"repoPath":"D:/repos/foo"}'` produces `cd D:/repos/foo && dotnet build`.

If the template declares `requiredParams`, missing values produce an error before execution. Otherwise, optional placeholders (`${other}`) are left untouched if not provided.

## Common Patterns

**Periodic build**
```
tool: instantiate_template
templateId: build-project
name: "Build SnakeGame"
intervalMinutes: 60
cwd: "D:/repos/SnakeGame"
```

**Custom monitor with inline script**
```
tool: create_automation
name: "Log watcher"
intervalMinutes: 10
script: "console.log('Checking logs...')"
scriptType: javascript
cwd: "C:/temp"
```

**One-shot heavy task**
```
tool: run_task
name: "Long test run"
command: "npm test"
timeoutMs: 600000
```

## Notes

- **Interpolation security** (v3.1.1+): Interpolated values must pass a character whitelist (alphanumeric, path separators, dots, spaces, tilde). Shell metacharacters are rejected.
- **Required params**: Templates can declare `requiredParams`. Missing values return an error instead of sending `${key}` literal to the shell.
- `intervalMinutes` must be a positive number. Very low values (< 1) will fire rapidly.
- The scheduler ticks every 30 seconds; the first run may happen within that window.
- Notifications accumulate until `ack_notifications` is called or the user reads them.
- Working directories are validated against an allowlist. System directories are blocked.

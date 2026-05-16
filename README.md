# Scheduler Templates Skill

Skill de Codex para automatizaciones con el `local-scheduler-mcp`.

## Qué hace

Provee al agente de Codex contexto especializado sobre cuándo y cómo usar las tools del `local-scheduler-mcp`. Incluye:

- **Built-in templates**: `build-project`, `disk-check`, `git-sync`
- **Flujo de decisión**: template vs. custom automation vs. one-shot task
- **Interpolación de parámetros**: `${key}` con JSON string
- **Patrones comunes**: builds periódicos, monitores, tareas de larga duración

## Instalación

```bash
cd ~/.codex/skills
git clone https://github.com/MauricioPerera/scheduler-templates-skill.git scheduler-templates
```

## Templates disponibles

| ID | Acción | Intervalo default | Uso |
|---|---|---|---|
| `build-project` | `dotnet build` | 60 min | Compilar proyectos .NET |
| `disk-check` | `Get-PSDrive C` | 5 min | Monitorear espacio en disco |
| `git-sync` | `git pull` | 30 min | Mantener repo sincronizado |

## Requiere

- [local-scheduler-mcp](https://github.com/MauricioPerera/local-scheduler-mcp) v3.1.0+
- Codex Desktop o cualquier agente con soporte MCP

## Estructura

```
scheduler-templates/
├── SKILL.md          # Contexto inyectado al LLM
└── README.md         # Documentación humana
```

## Licencia

MIT

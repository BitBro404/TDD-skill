# Flujo Dev TDD — Multi-repositorio

Skill para coordinar análisis, planificación aprobada, implementación TDD, memoria y handoff entre agentes/modelos (Claude Code, OpenCode, Codex/ChatGPT).

## Características

- Workspaces con varios repositorios Git, `plan.md` coordinador y memoria/handoff por repo.
- TDD estricto (RED → GREEN → REFACTOR) con aprobación explícita del plan.
- **Economía de código (estilo [Ponytail](https://github.com/DietrichGebert/ponytail)):** mínimo código necesario, reutilizar antes de escribir, sin abstracciones especulativas, sin recortar validación, seguridad ni accesibilidad.
- **Desarrollo web moderno ([Modern Web Guidance](https://developer.chrome.com/docs/modern-web-guidance)):** objetivo Baseline, plataforma nativa primero, mejora progresiva y fallbacks ligeros.
- **SQL mínimo:** evitar tablas y migraciones; si son inevitables, justificación obligatoria en el plan.
- **Tareas no realizables:** explicar el motivo y ofrecer alternativas.
- **Pragmatismo y estoicismo** explícitos en cada plan.

## Rutas incluidas

- `.claude/skills/flujo-dev-tdd/SKILL.md`
- `.agents/skills/flujo-dev-tdd/SKILL.md`
- `.opencode/skills/flujo-dev-tdd/SKILL.md`

Las tres copias contienen el mismo contenido.

## Instalación global

| Agente | Ruta |
|---|---|
| Claude Code | `~/.claude/skills/flujo-dev-tdd/SKILL.md` |
| OpenCode | `~/.config/opencode/skills/flujo-dev-tdd/SKILL.md` |
| Codex (ChatGPT) | `~/.agents/skills/flujo-dev-tdd/SKILL.md` (o `~/.codex/skills/` en versiones antiguas) |

Para un solo proyecto, copia las carpetas `.claude/`, `.opencode/` o `.agents/` en la raíz del workspace.

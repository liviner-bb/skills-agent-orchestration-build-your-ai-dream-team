# Agent Team for Mona's Project Pulse Dashboard

## Overview

Four custom agents collaborate to build the Project Pulse dashboard.
The **Orchestrator** delegates work to three specialists — **Planner**,
**Coder**, and **Designer** — following an explicit phase-based execution model.

## Agents
Definintions for the below agents can be found in .github/agents

| Agent | Model | Role |
|-------|-------|------|
| **Orchestrator** | Claude Opus 4.7 | Breaks down requests into phases, delegates to specialists, and verifies the integrated result. Never implements code itself. |
| **Planner** | Claude Opus 4.7 | Researches the codebase and produces ordered implementation plans with file assignments, dependencies, and edge cases. Does not write code. |
| **Coder** | GPT-5.5 | Implements logic, fixes bugs, creates support files (e.g. `.vscode/launch.json`), and validates changes. Stays within assigned file scope. |
| **Designer** | Gemini 3.1 Pro | Handles UI/UX — styling, accessibility, information hierarchy, responsive layout, and visual polish. Stays within assigned file scope. |

## Execution Flow

```
User prompt
    └─▸ Orchestrator
            ├─▸ Planner   → produces plan (steps, file scopes, dependencies)
            ├─▸ Coder     → implements code per plan phase
            └─▸ Designer  → creates styling & UX per plan phase
```

1. The Orchestrator requests a plan from the Planner.
2. It parses the plan into phases with explicit file assignments.
3. Non-overlapping work runs in parallel; dependent work runs sequentially.
4. Each specialist reports what changed and any remaining risks.
5. The Orchestrator verifies integration and reports the final outcome.

## Key Conventions

- **File-scoped ownership** — each agent only touches files explicitly assigned to it.
- **No git operations** — agents never stage, commit, or push. The learner controls git via Copilot CLI prompts.
- **Deterministic outputs** — names, ports, and paths are predictable and repeatable.
- **Design expectations** — the Designer produces a polished dashboard with cards, badges, spacing, and responsive behavior, not bare HTML.

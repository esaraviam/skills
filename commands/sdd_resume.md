# Command: /sdd_resume
# Description: Retoma un pipeline de SDD activo leyendo el estado actual de progress.json.

You are a strict SDD orchestrator resuming an interrupted development pipeline. 

Please execute the following steps immediately:
1. Scan the `.sdd/tasks/` directory to understand the current project state.
2. Identify the active specification by reading the reference inside the task files, and find its corresponding technical contract in `documentation/architecture_<spec_id>.md`.
3. List the completed tasks and highlight the first "pending" task in the terminal.
4. Open the local skill file required for that pending task (e.g., `.claude/skills/backend-coder.md`) and strictly apply its constraints.
5. Ask for my [APPROVAL] to execute this specific task with a clean, isolated context payload.

Let's resume. Scan the .sdd/tasks/ directory and output the current status matrix.

# Command: /sdd
# Description: Ejecuta el pipeline estricto de Spec-Driven Development usando los archivos de skills locales en .claude/skills/.

You are a principal systems engineer supervising a strict Spec-Driven Development (SDD) pipeline. You must orchestrate our development flow by reading our local skill contracts to process the feature spec file provided in the arguments.

If the user did not provide a filename, explicitly ask: "¿Qué archivo de especificación en /specs deseas procesar?".

Target File Configuration:
- Input Spec Path: "specs/<ARGUMENT_PROVIDED>"
- Documentation Output Path: "documentation/architecture_<ARGUMENT_PROVIDED>"

Please execute the following pipeline phases strictly in order, waiting for my explicit [APPROVAL] between phases.

### Phase 1: Technical Architectural Design (Skill: architecture-expert.md)
1. Read the input business specification from the target file in `specs/`.
2. Open and strictly follow the rules, boundaries, and instructions defined in your local file `.claude/skills/architecture-expert.md`. Do not assume external roles.
3. Design the technical solution (database schemas, API contracts, backend logic) without writing application source code.
4. Write the final structural design contract into the `documentation/` folder, naming it `architecture_<ARGUMENT_PROVIDED>`.
5. Present a summary of the technical design and wait for my [APPROVAL] to proceed to Phase 2.ß

### Phase 2: Backlog Decomposition & Graph Generation (team-coordinator.md)
1. Read the approved technical contract in `documentation/`.
2. Open `.claude/skills/team-coordinator.md`.
3. Break down the architecture into atomic tasks, but instead of a central file, create the directory `.sdd/tasks/` if it doesn't exist.
4. Write each task into its own file (e.g., `.sdd/tasks/task_01.json`). 
5. *CRITICAL:* For every task, explicitly populate the `"depends_on": []` array with the IDs of the foundational tasks that MUST be completed before this one can start (e.g., frontend mockups might depend on API contracts).
6. *CRITICAL:* Add a `"read_architecture_section"` field to each task JSON. Specify the exact heading or section name from the `documentation/architecture_<ARGUMENT_PROVIDED>` file that contains the necessary context for this specific task (e.g., "## 4. Business Logic & Constraints -> Auth Rules").
7. Present the full graph overview in the terminal and wait for [APPROVAL].

### Phase 3: Parallel Execution via Task Locking & Dependency Check
1. Scan the `.sdd/tasks/` directory and analyze all available JSON files.
2. **Dependency Resolution Rule:** Filter out any task where `"status": "pending"` BUT the tasks listed in `"depends_on"` are NOT yet marked as `"completed"`.
3. **Task Locking Rule (Prevent Race Conditions):** Out of the unblocked pending tasks, find the first one that does NOT have an active lock. Immediately update its status field to `"in_progress"` and write your unique session ID or a timestamp to lock it (e.g., `"status": "in_progress_lock"`). This claims the task for this specific terminal window.
4. Open the required local skill file from `.claude/skills/` and execute the work using the Lazy Loading rule. **CRITICAL CONTEXT RULE:** You MUST read ONLY the specific section of the architecture document indicated in the `"read_architecture_section"` field of the task JSON. Do NOT read the entire architecture file to save context window.
5. Once your code output is successfully patched, update that specific file's status to `"completed"`.
6. **Context Purge & Loop:** Before proceeding to the next unblocked task, YOU MUST pause and instruct the user: "Por favor, ejecuta `/compact` o `/clear` para limpiar mi contexto antes de continuar con la siguiente tarea". Once the user confirms the context is cleaned, loop back to Step 1.

Let's begin with Phase 1. Prompt me for the filename if it wasn't captured, read the file, and apply the architecture-expert skill.

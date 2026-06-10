---
name: openspec-propose
description: >
  Generates a structured OpenSpec change folder for any new feature, fix, or
  refactor. Use this skill whenever the user types /propose [name], says "quiero
  proponer un cambio", "nueva propuesta", "propón un cambio para", "let's spec
  this out", "create a proposal for", or starts describing a feature they want
  to build before writing any code. Also trigger when the user says "quiero
  documentar este cambio", "necesito una spec para", or asks to plan something
  before implementing it. Always use this skill proactively when the user is
  about to start a non-trivial feature — it is better to over-trigger than to
  miss the opportunity to structure the work.
---

# OpenSpec Propose Skill

You generate the full OpenSpec change structure before any code is written.
Your job is to make the intent explicit, the requirements unambiguous, and
the implementation plan reviewable — then hand off to the right skills.

## Spec-Generation Boundary (Strict)
- **Zero-Code Policy:** You are strictly forbidden from writing application source code, UI styles, infrastructure configurations, or implementation details of any kind. Your output is exclusively structured specification artifacts (`.md` files).
- **Scoped Delegation:** You are permitted to invoke `software-architect` as a single, bounded delegation solely for the production of `design.md`. This is the only cross-skill call you may make. You may not chain additional skill invocations or coordinate a multi-step workflow beyond this.
- **Lifecycle Boundary:** You operate at the pre-implementation stage only. You do not allocate implementation tasks, manage development phases, or make decisions about feature prioritization. Once the OpenSpec folder is generated and reviewed, your execution context terminates.
- **Immutable Output Contract:** Your deliverables are exclusively: `proposal.md`, `specs/requirements.md`, `design.md` (via `software-architect`), and `tasks.md`. Nothing else.

---

## What You Produce

For every proposal you create this folder structure in the repo:

```
openspec/changes/<change-name>/
├── proposal.md      — intent, context, scope
├── specs/
│   └── requirements.md  — GIVEN/WHEN/THEN scenarios + acceptance criteria
├── design.md        — technical approach (delegated to software-architect)
└── tasks.md         — ordered implementation checklist
```

---

## Step-by-Step Workflow

### Step 1 — Clarify (if needed)

Skip this step if you can answer all of these from the user's description:
- What is the core user-facing change?
- Which layer is affected (frontend / backend / infra / data)?
- What is the motivation or business reason?

If any of these is missing AND the description is under 2 sentences, ask ONE targeted question.
Never ask more than one question before proceeding.

### Step 2 — Generate `proposal.md`

```markdown
# Proposal: <change-name>

## Status
Draft | In Review | Approved | Archived

## Context
Why this change is needed. What problem it solves. What happens if we don't do it.

## Scope
What IS included in this change.
What is explicitly OUT OF SCOPE.

## Impact
- Skills / layers affected: [frontend | backend | infra | auth | data]
- Breaking changes: yes / no
- Estimated complexity: low / medium / high
```

### Step 3 — Generate `specs/requirements.md`

Write requirements as GIVEN/WHEN/THEN scenarios. Aim for 3–6 scenarios
that cover the happy path + at least one edge case.

```markdown
# Requirements: <change-name>

## Functional Requirements

### Scenario 1: <short title>
- GIVEN <precondition>
- WHEN <action>
- THEN <expected outcome>

### Scenario N: ...

## Non-Functional Requirements
- Performance: ...
- Security: ...
- Accessibility: ...

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

### Step 4 — Delegate `design.md` to software-architect

**Always invoke the `software-architect` skill to produce `design.md`.**
If you need to reference the skill file directly, locate it using the Glob tool
with pattern `**/software-architect/SKILL.md` from the skills root.
Follow its instructions to produce the technical design. The
design must include:

- Architecture decision (what pattern / layer is affected)
- Component diagram or data flow (Mermaid preferred)
- Key interfaces or contracts changed
- Risk assessment (what could break)
- Alternatives considered and why they were rejected

Save output as `openspec/changes/<change-name>/design.md`.

### Step 5 — Generate `tasks.md`

Break implementation into atomic, ordered tasks. Each task must be:
- Small enough to implement in one session
- Independently testable
- Assigned to a skill (frontend | backend | security | ux | infra)

```markdown
# Tasks: <change-name>

## Implementation Order

| # | Task | Skill | Files | Estimate |
|---|------|-------|-------|----------|
| 1.1 | Description | backend | path/to/file.ts | S |
| 1.2 | ... | frontend | ... | M |

## Size Reference
S = < 1hr | M = 1–3hr | L = 3–8hr | XL = needs breakdown

## Gate Conditions
Before marking this change as ready to implement:
- [ ] proposal.md approved
- [ ] All GIVEN/WHEN/THEN scenarios reviewed
- [ ] design.md reviewed by a human
- [ ] tasks.md ordered and sized
```

### Step 6 — Summary to user

After generating all files, present:

```
✅ openspec/changes/<change-name>/
   ├── proposal.md       — [1-line summary of intent]
   ├── specs/requirements.md — [N scenarios, N acceptance criteria]
   ├── design.md         — [key architectural decision made]
   └── tasks.md          — [N tasks, total estimate]

Next step: review the files, then run /opsx:apply or hand off to spec-driven-orchestrator.
```

---

## Integration with Other Skills

| Situation | Action |
|-----------|--------|
| Change has UI impact | After design.md, note that `ux-design-expert` should be consulted for spec/design of UI flows |
| Change touches auth, data exposure, or external APIs | Flag in `proposal.md` and add a security review task in `tasks.md` for `ai-security-expert` |
| Ready to implement | Instruct user to trigger `spec-driven-orchestrator` with the generated spec as input |
| CSS/styles involved | Add a BEM review task using `bem-refactor` skill |

---

## Quality Rules

- Never write code in this skill — only specs, design, and tasks
- Never skip `design.md` — always delegate to `software-architect`
- `tasks.md` tasks must never be "implement the feature" — always atomic
- If a task is XL, break it into sub-tasks before finalizing
- proposal.md must have explicit OUT OF SCOPE section — this prevents scope creep

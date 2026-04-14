---
name: bem-refactor
description: >
  Scans CSS, Sass (.sass), and SCSS (.scss) files in a project and automatically
  refactors class names to follow the BEM (Block–Element–Modifier) naming convention.
  Also updates all class name references across JSX and TSX files to stay in sync.
  Use this skill whenever the user asks to: convert styles to BEM, rename CSS classes
  to BEM format, clean up or standardize a stylesheet, refactor SCSS to BEM, or fix
  inconsistent class naming in a project. Trigger even if the user just says "apply BEM
  to my project" or "make my styles follow BEM" without mentioning the skill by name.
---

# BEM Refactor Skill

Refactors CSS/Sass/SCSS class names to follow BEM (Block–Element–Modifier) conventions
and updates all matching references in JSX/TSX files. Always shows a diff before applying.

---

## BEM Naming Rules (reference)

- **Block**: Standalone component. `.card`, `.nav`, `.button`
- **Element**: Part of a block, joined with `__`. `.card__title`, `.nav__item`
- **Modifier**: Variant or state, joined with `--`. `.card--featured`, `.button--disabled`
- **Combined**: `.card__title--highlighted`

---

## Workflow

### Step 1 — Discover files

Use the Glob tool to discover files, excluding `node_modules` and `vendor`:

- Style files: pattern `**/*.css`, `**/*.scss`, `**/*.sass` (exclude `*.min.css`)
- JSX/TSX files: pattern `**/*.jsx`, `**/*.tsx`

Run separate Glob calls for each pattern. Glob results are sorted by modification time and respect tool permissions — do not use `find` or `rg` for this step.

Before processing each style file, check for the ignore directive:
- If the file contains `// @bem-ignore` (anywhere), skip it entirely and note it in the log.

---

### Step 2 — Parse and infer BEM structure

For each style file, extract all class selectors and determine their role (Block, Element, or Modifier) using this priority:

#### 2a. Sass/SCSS nesting (preferred)

Look for indented or `{}` nesting. A class nested inside another is an Element or Modifier of the parent Block.

```
.card {           → Block
  .title { }      → card__title  (nested = element)
  .title-big { }  → card__title--big  (nested + modifier pattern)
  &:hover { }     → skip (pseudo, not a class)
  &.is-active { } → card--active  (& + modifier = modifier)
}
```

Parse `.sass` files using indentation depth; parse `.scss` files using `{` / `}` brace counting.

#### 2b. CSS selector analysis (fallback for flat CSS)

When no nesting is found, infer from selector patterns:

| Pattern | Interpretation |
|---|---|
| `.block` (simple, standalone) | Block |
| `.block .block-element` (descendant) | block__element |
| `.block-element` (prefix matches a known block) | block__element |
| `.is-*`, `.has-*`, `.active`, `.disabled` | modifier (attach to parent block) |
| Ambiguous (no clear parent) | Best-guess Block, log it |

#### 2c. Ambiguity handling

When a class cannot be confidently assigned:
1. Apply best-guess (e.g., treat as Block if top-level, Element if it shares a prefix with a known Block)
2. Add it to the **ambiguity log** with: original name, guessed BEM name, and reason

---

### Step 3 — Build a rename map

Produce a mapping of every old class name → new BEM class name. Example:

```
card-title        → card__title
card-title-big    → card__title--big
btn               → button  (if context suggests full name)
btn-primary       → button--primary
is-active         → [parent]--active
```

Group renames by Block so they're easy to review.

---

### Step 4 — Generate and display the diff

Before writing anything, show a unified diff per file:

```diff
--- src/components/Card.scss
+++ src/components/Card.scss (BEM)
-  .card-title {
+  .card__title {
     font-size: 1.2rem;
   }
-  .card-title-big {
+  .card__title--big {
     font-size: 1.6rem;
   }
```

Also show the JSX/TSX diffs:

```diff
--- src/components/Card.tsx
+++ src/components/Card.tsx (BEM)
-  <h2 className="card-title card-title-big">
+  <h2 className="card__title card__title--big">
```

Then show the **ambiguity log** if any entries exist:

```
⚠️  Ambiguity log (12 items — review these):
  is-active     → card--active   (assumed parent: .card)
  highlight     → highlight      (treated as Block — no clear parent found)
```

Ask the user: "Here's the full diff. Apply these changes? (yes / no / adjust)"

- **yes** → proceed to Step 5
- **no** → discard all changes, report "BEM refactor cancelled — no files were modified"
- **adjust** → discuss specific renames, rebuild the diff, re-present before applying

---

### Step 5 — Apply changes

Once the user confirms:

1. **Style files first**: Apply renames to CSS/Sass/SCSS using string replacement (respect selector context, avoid renaming string literals or comments unintentionally).
2. **JSX/TSX files second**: Update `className="..."` strings and `className={...}` template literals. Handle:
   - `className="card-title"`
   - `className={\`card-title ${extra}\`}`
   - `className={styles['card-title']}` → `className={styles['card__title']}`
   - `clsx('card-title', ...)` / `cx(...)` patterns

3. Confirm how many files were modified and how many class names were renamed.

---

### Step 6 — Summary report

After applying, print a summary:

```
✅ BEM refactor complete
   Style files updated:  8
   JSX/TSX files updated: 14
   Classes renamed:       47
   Ambiguous (logged):    3

⚠️  Ambiguous classes (manual review recommended):
   highlight → highlight (Block — no clear parent)
   ...
```

---

## Edge cases to handle

- **Already-BEM classes**: If a class already uses `__` or `--`, skip it (don't double-transform).
- **Utility classes**: `.flex`, `.hidden`, `.mt-4` — these are usually not BEM. If they don't match any block prefix and appear to be utility/atomic, skip and log.
- **Multiple classes on one element**: Update each class independently.
- **CSS Modules**: If files use CSS Modules syntax (`:local(.name)`), handle the inner class name.
- **`@apply` in Tailwind**: If `@apply card-title` appears in a style file, rename it too.
- **Dynamic class names**: If a JSX class is fully dynamic (`className={getDynamicClass()}`), skip and warn.

---

## Notes for Claude

- Always read the full file before renaming — context matters for correct Block inference.
- When processing `.sass` files, use indentation (2 or 4 spaces) as the nesting signal.
- When processing `.scss` files, track `{` / `}` depth.
- The rename map must be built for the entire project before any files are touched, so cross-file references stay consistent.
- If the user's project is large (50+ files), summarize the diff at a high level first, then offer to show per-file details on request.

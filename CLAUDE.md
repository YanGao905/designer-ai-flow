# Claude Code Guidelines for Forms-Web Designers

This file is for designers using Claude Code to fix design craft bugs in Forms-Web.

## Important: All Changes Stay Local by Default

**Never create a branch, commit, or file a PR unless the user explicitly asks you to.**

All code changes are local only until the user says something like "help me file a PR" or "commit this". Do not proactively suggest committing or pushing. The user decides when the fix is ready to ship.

---

## Step 1: Before Making Any Change — Check Impact Scope

Before touching any component, always run a grep to find every file that references it:

```bash
grep -r "ComponentName" Forms/scripts --include="*.tsx" --include="*.ts" -l
```

Then tell the designer **exactly where to see each affected UI in the product**, so they can visually verify nothing is broken.

### Example — CopilotRefineToolsBar

If the change touches `CopilotRefineToolsBar`, it affects 5 Copilot features. The designer must test each one in the local dev environment:

| # | Feature | How to trigger | What to check |
|---|---------|---------------|---------------|
| 1 | **Generate Form** | New form → type a description in Copilot → AI generates form | Toolbar appears at bottom. Check **"Keep it"** button: icon and text vertically aligned |
| 2 | **Rewrite Content** | Design page → click a question → Copilot menu → "Rewrite" | Toolbar appears inline below question. Check **"Replace"** button (↩ icon + text) |
| 3 | **Polish / Refine Form** | Design page → Copilot Polish → enter prompt → AI generates | Toolbar appears below result. Check **"Append" / "Replace"** button (↩ icon + text) |
| 4 | **Continue Writing** | Design page → select a question → trigger Continue Writing | Toolbar appears below generated content. Check **"Keep"** button: icon and text vertically aligned |
| 5 | **Answer Explanation** | Create a Quiz → select a question → trigger Answer Explanation | Toolbar appears below explanation. Check **"Insert"** button (↩ icon + text) |

Always provide this kind of checklist — not just "6 files are affected", but **which screen, which action, which button to look at**.

---

## Step 2: Branch Rules

- Every fix gets its own branch. Never mix multiple fixes in one branch.
- Branch naming: `users/<alias>/<short-description>`
  - Example: `users/yangao1/keep-button-alignment`
- Start each branch from a clean base (`features/dev`), not from another fix branch.

---

## Step 3: PR Rules

- Target branch: always `features/dev`, never `main`
  - `main` requires IM approval and does not trigger builds automatically
- Use `az repos pr create` (ADO repo, not GitHub — `gh` command does not work here)
- After creating the PR, manually link the ADO work item in the PR's Work Items section

---

## Step 4: Before Committing

Run TypeScript check to catch any type errors before submitting:

```bash
cd Forms && npx tsc --noEmit
```

Also confirm only the intended files are modified:

```bash
git diff --name-only
```

---

## Step 5: After PR is Merged

Manually update the bug status to **Resolved** in ADO. It does not update automatically.

---

## Common Pitfalls

| Pitfall | What happens | How to avoid |
|--------|-------------|-------------|
| Shared component with wide reach | Fixing one button breaks 5 other features | Always grep first, build a visual checklist |
| Multiple fixes in one branch | PR is hard to review and hard to revert | One fix = one branch, always |
| PR targeting `main` | Build doesn't trigger, needs IM approval | Default to `features/dev` |
| Missing curly braces / type errors | CI fails, PR cannot merge | Run `npx tsc --noEmit` before committing |
| Forgetting to link work item | Bug stays open after fix is shipped | Link in PR page, update to Resolved after merge |
| AI suggests extra "improvements" | Change scope creeps beyond the original fix | Tell Claude explicitly: fix only this one thing |

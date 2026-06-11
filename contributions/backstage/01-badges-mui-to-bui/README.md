# Contribution 1: Migrate `badges` workspace from Material UI to Backstage UI

**Contribution Number:** 1  
**Student:** Pushkar Wani  
**Issue:** https://github.com/backstage/community-plugins/issues/7869 (tracked under https://github.com/backstage/community-plugins/issues/7023)  
**Status:** Phase I — In Progress

---

## Why I Chose This Issue

I chose this issue because it sits inside a large, well-run open-source project (Backstage community plugins) and gives me a clearly scoped, self-contained task: migrating a single workspace off Material UI (MUI) and onto the new Backstage UI (BUI) component library. The tracker explicitly asks for one PR per workspace, so the surface area is small enough to finish well rather than getting lost in a sprawling monorepo change.

It also matches what I want to learn: real-world React + TypeScript work, how a mature design-system migration is carried out incrementally, and the conventions a project uses for component libraries, styling (CSS Modules with design tokens), and contribution hygiene (changesets, signed commits, lint/build/typecheck gates). The `badges` workspace has only one MUI file, which makes it a realistic but approachable first contribution.

---

## Understanding the Issue

### Problem Description

The Backstage community is migrating plugins away from Material UI (`@material-ui/*`) to the project's own Backstage UI library (`@backstage/ui`, "BUI"). The `badges` workspace has not yet been migrated — it still imports MUI components directly. The goal is to replace all MUI usage with BUI equivalents while preserving the existing behavior and appearance of the Entity Badges dialog.

### Expected Behavior

The `badges` plugin renders the same Entity Badges dialog and behaves identically, but is built entirely on `@backstage/ui` components and CSS Modules (with BUI design tokens) instead of MUI components and `makeStyles`. No `@material-ui/*` imports should remain in the workspace.

### Current Behavior

The plugin works, but it depends on Material UI. There is exactly one MUI source file:
`workspaces/badges/plugins/badges/src/components/EntityBadgesDialog.tsx`, which imports `Box`, `Button`, `Dialog`, `DialogActions`, `DialogContent`, `DialogContentText`, `DialogTitle`, `Typography`, `Select`, `MenuItem`, `FormControl`, `InputLabel`, `useMediaQuery`, and `useTheme`/`styles` from `@material-ui/core`.

### Affected Components

- `workspaces/badges/plugins/badges/src/components/EntityBadgesDialog.tsx` — the only file with MUI imports
- `workspaces/badges/plugins/badges/package.json` — dependency changes (remove `@material-ui/*`, add `@backstage/ui`)
- `workspaces/badges/.changeset/` — new changeset for the migration

---

## Reproduction Process

### Environment Setup

```bash
cd workspaces/badges
yarn install
yarn start   # run the plugin in isolation
```

The workspace is run in isolation rather than building the whole monorepo, per the project's per-workspace contribution model.

### Steps to Reproduce (confirm current MUI usage)

1. `cd workspaces/badges`
2. Search for MUI imports: `grep -rl "@material-ui\|@mui" plugins`
3. Observed result: `EntityBadgesDialog.tsx` (plus README/CHANGELOG/package.json references) still depend on Material UI; `@backstage/ui` is not yet a dependency and no migration changeset exists.

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork — TBD]
- **Screenshots/logs:** [Dialog screenshot before migration — TBD]
- **My findings:** The migration is genuinely "Not Started." Only one source file uses MUI, the dialog is a modal with a style `Select`, badge previews, and a close `Button` — a manageable scope for a single PR.

---

## Solution Approach

### Analysis

The root "cause" is simply that this workspace predates the BUI migration effort. The fix is a like-for-like component swap: every MUI component has a BUI (or react-aria-components) equivalent, and inline/`makeStyles` styling moves to CSS Modules using BUI CSS variables.

### Proposed Solution

Rewrite `EntityBadgesDialog.tsx` to use `@backstage/ui` components, replace `makeStyles`/inline styles with a `.module.css` file using BUI tokens, remove all `@material-ui/*` imports and dependencies, then verify with typecheck/build/lint and add a changeset.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Replace all Material UI usage in the `badges` workspace with Backstage UI, keeping the Entity Badges dialog's behavior and look unchanged.

**Match:** Reference completed migrations in the same repo:
- `workspaces/announcements`
- `workspaces/azure-devops/plugins/azure-devops`
- `workspaces/linguist/plugins/linguist`
- `workspaces/manage/plugins`

**Plan:**
1. Add `@backstage/ui` to `plugins/badges/package.json`; remove `@material-ui/*`.
2. Migrate `EntityBadgesDialog.tsx` component by component:
   - `Typography` → `<Text variant="body-medium">`
   - `Box` (with margins) → `<Flex>` / `<Box>` with BUI spacing tokens
   - `Button` → BUI `<Button variant="primary">` (`disabled` → `isDisabled`)
   - `Dialog`/`DialogTitle`/`DialogContent`/`DialogActions`/`DialogContentText` → BUI dialog / react-aria-components dialog primitives
   - `FormControl`/`InputLabel`/`Select`/`MenuItem` → BUI `<Select>`
   - `useTheme`/`useMediaQuery` breakpoint logic → BUI-supported responsive approach
3. Move inline styles to `EntityBadgesDialog.module.css` using BUI CSS variables (`--bui-space-*`, etc.); MUI icons → Remix icons (`@remixicon/react`) if any are introduced.
4. Update tests as needed.
5. Run `yarn tsc`, `yarn build`, `yarn lint`.
6. Create a changeset: `yarn changeset`.

**Implement:** [Link to your branch/commits as you work — TBD]

**Review:** Self-review checklist — no `@material-ui/*` imports remain; behavior/visuals unchanged; one-PR-per-workspace respected; commits signed (`Signed-off-by`).

**Evaluate:** Run the workspace with `yarn start` and confirm the dialog opens, the style `Select` updates badge previews, snippets copy, and the close button works.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: Dialog renders title, content text, and close button
- [ ] Test case 2: Selecting a badge style updates the fetched badge specs
- [ ] Test case 3: Loading and error states render `Progress` / `ResponseErrorPanel`

### Integration Tests

- [ ] Badge previews render an image and a copyable Markdown snippet per badge
- [ ] Responsive behavior (full-screen dialog on small viewports) is preserved

### Manual Testing

[Run `yarn start`, open the dialog, switch styles, verify previews and copy buttons — results TBD]

---

## Implementation Notes

### Week 1 Progress

Set up the workspace, reproduced the current MUI dependency, scoped the single affected file (`EntityBadgesDialog.tsx`), and reviewed reference migrations in the repo. Drafted the component-by-component migration plan above.

### Week 2 Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** `EntityBadgesDialog.tsx`, `package.json`, new `EntityBadgesDialog.module.css`, new changeset (planned)
- **Key commits:** [Links to important commits — TBD]
- **Approach decisions:** Like-for-like component swap; CSS Modules with BUI tokens instead of `makeStyles`; keep behavior and visuals unchanged

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted — TBD]

**PR Description:** Migrate the `badges` workspace from Material UI to Backstage UI (closes part of #7023, addresses #7869). Single-workspace PR; no behavior changes.

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** Not yet submitted

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically — TBD]

### Challenges Overcome

[What was hard and how you solved it — TBD]

### What I'd Do Differently Next Time

[Reflection on your process — TBD]

---

## Resources Used

- Backstage UI (BUI) migration tracker: https://github.com/backstage/community-plugins/issues/7023
- Badges migration issue: https://github.com/backstage/community-plugins/issues/7869
- Migration guide (in repo): `workspaces/badges/.claude/skills/mui-to-bui-migration/SKILL.md`
- Reference migrations: `workspaces/announcements`, `workspaces/azure-devops/plugins/azure-devops`, `workspaces/linguist/plugins/linguist`, `workspaces/manage/plugins`

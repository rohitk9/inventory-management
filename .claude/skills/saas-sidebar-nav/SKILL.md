---
name: saas-sidebar-nav
description: Redesign a Vue 3 app's horizontal top navigation into a modern SaaS-style vertical left sidebar with consistent spacing and design tokens. Use this skill when asked to add a sidebar, convert top-nav to a sidebar layout, or give the app a more "modern SaaS" / "polished professional" look.
---

# SaaS Sidebar Navigation Redesign

This skill converts a Vue 3 application's horizontal top-nav layout into a
vertical left-sidebar layout in the style of modern SaaS products (e.g.
Linear, Stripe Dashboard, Vercel). It is written to be **portable across Vue
3 codebases** — it makes no assumption about exact file paths, route names,
or component names. Always discover the project's actual structure before
editing anything.

The end result should look like a deliberate design system, not a
find-and-replace: a fixed-width sidebar, tokenized colors/spacing, and no
leftover layout debt (e.g. hardcoded pixel offsets that assumed a horizontal
bar).

## Discover project conventions before touching anything

Before writing a single line, figure out **how this project wants changes
made**:

1. Read the project's `CLAUDE.md` (repo root) if present. Look specifically
   for a "Subagents" or "Agents" section that names a Vue/frontend
   specialist subagent and any **mandatory delegation rule** attached to it
   (e.g. "any time you modify a `.vue` file you MUST delegate to X").
2. List `.claude/agents/*.md` and read the frontmatter `name`/`description`
   of each file to see if one is scoped to Vue/frontend work, even if
   CLAUDE.md didn't mention it explicitly.
3. Decide your execution mode:
   - **A Vue specialist subagent exists** → every `.vue` file create/edit in
     this workflow MUST be delegated to that subagent via the Task tool.
     Do not edit `.vue` files directly yourself in this mode. Pass it clear,
     scoped instructions per step (see "Implementation steps" below) rather
     than one giant instruction — that keeps each change reviewable.
   - **No Vue specialist subagent exists** → proceed directly, following
     Vue 3 + Composition API best practices, scoped `<style>` blocks, and
     whatever component conventions the project already uses (see below).
4. Regardless of mode, check for a read-only review subagent (e.g. a
   `code-reviewer`-style agent) to run after the changes are made (see
   "Verification").

Do not hardcode `client/src/App.vue` or any other specific path anywhere in
your working plan — go find the real one (see next section).

## Analyze current navigation & layout

Locate and read, in order:

1. **The app root component.** Usually the file mounted in `main.js`/
   `main.ts` (look for `createApp(...)`) — commonly `App.vue`, but confirm
   rather than assume. This is almost always where the top-level flex
   layout and the nav markup live.
2. **The nav markup itself** — find the element(s) containing the primary
   navigation links (often `<router-link>` or `<a>` tags in a `<nav>` or
   `<header>`). Note:
   - How the active route is currently highlighted — ad hoc
     (`:class="{ active: $route.path === '/x' }"`) or via Vue Router's
     built-in `active-class` / `exact-active-class` props on
     `<router-link>`.
   - Any elements pinned to the far right of the bar (profile menu,
     language/locale switcher, notifications, search) — these need a new
     home in the sidebar (header or footer slot), not to be dropped.
3. **The route table** (`router.js`/`router/index.js`/wherever
   `createRouter` is called) — build the authoritative list of nav
   destinations from routes, not just from what's currently rendered as a
   link. Flag (but don't fix, unless asked) any route with no nav entry or
   any nav entry with no matching route — call this out to the user as an
   aside.
4. **Where global/shared styles live.** Many small Vue apps put a global
   unscoped `<style>` block in the root component containing shared classes
   (cards, tables, badges, page headers, etc.) rather than a separate CSS
   file. Find it — this is where design tokens will be introduced.
5. **Sticky/fixed positioning couplings.** Search the codebase (`grep -rn
   "position: sticky\|position:sticky\|position: fixed"`) for any element
   whose `top` offset is hardcoded to the current nav's height (e.g. a
   filter bar or secondary toolbar sitting at `top: <nav-height>px`). These
   assume a horizontal bar of fixed height sits above them; once the nav
   becomes a sidebar, that element becomes a normal top-of-column bar and
   its offset must become `top: 0` (or the sticky positioning removed
   entirely if it no longer needs to stick).
6. **Existing tokenization.** Check whether CSS custom properties
   (`--color-*`, `--spacing-*`, etc.) already exist in `:root`. If none
   exist, note the hex colors and spacing values actually used (see "Design
   tokens" below) — don't invent a new palette.
7. **Per-view style overrides.** In each view/page component, check its
   `<style scoped>` block for class names that also exist in the global
   stylesheet (e.g. a view-local `.card-header { padding: ... }` that
   silently overrides the global `.card-header`). These are landmines: once
   spacing is tokenized/normalized globally, a stray local override will
   make one page look inconsistent with the rest. Inventory them now so
   they can be reconciled in the normalization step.

## Sidebar design spec

Use this as the target shape, adapted to what the project's routes/branding
actually are:

- **Width**: fixed `240px`–`280px` (`260px` is a good default) on desktop.
  Do not make it fluid/percentage-based — SaaS sidebars are a fixed rail.
- **Structure**, top to bottom:
  1. **Brand/logo header** — existing logo/company name, same content as
     today's top-nav logo, just re-oriented to sit at the top of the
     sidebar instead of the left of a bar.
  2. **Primary nav list** — one entry per route, each with: an icon
     (reuse the project's existing icon approach — see "Preserve existing
     conventions" below), a text label, and a clear active/hover state.
     Stack vertically, full-width clickable rows (not small pill buttons),
     `~8px` vertical gap between rows.
  3. **Optional footer** — pinned to the bottom of the sidebar (`margin-top:
     auto` inside a flex column) for account-level controls: profile
     menu, language/locale switcher, settings, sign-out. This is where
     any component currently pinned to the far right of a horizontal bar
     via `margin-left: auto` should move to.
- **Active-state pattern**: prefer Vue Router's built-in `active-class`
  (and `exact-active-class` for the root `/` route, since it would
  otherwise match every path as a prefix) over ad hoc
  `:class="{ active: $route.path === '/x' }"` comparisons on every link —
  it's less code and self-maintaining as routes change. **However**, if
  the project already uses the ad hoc pattern consistently, keep that
  pattern for this change (don't mix conventions) and just note the
  built-in alternative as a suggestion to the user rather than silently
  switching styles mid-refactor.
- **Layout mechanics**: change the app root from a column flex
  (`flex-direction: column` with the nav stacked above `<main>`) to a row
  flex (`display: flex; flex-direction: row`) with the sidebar as a
  fixed-width flex child (`flex: 0 0 260px`) and the content column as the
  flexible remainder (`flex: 1 1 auto; min-width: 0; overflow-x: auto`).
  The content column keeps its own internal vertical stack (secondary
  toolbar/filter bar, then routed content) — that internal structure
  should NOT change, only what sits to its left.
- **Full height**: sidebar should span full viewport height
  (`height: 100vh; position: sticky; top: 0` or `position: fixed` +
  matching content offset — pick whichever matches how the rest of the
  app already handles scroll containers) so it doesn't scroll away.
- **Responsive behavior** (no UI library assumed — do this with plain
  CSS/media queries and, if needed, a small ref-based toggle):
  - Above a breakpoint (e.g. `1024px`): full sidebar with icon + label.
  - Below that breakpoint, on tablet-ish widths: collapse to an
    **icon-only rail** (~64px wide, labels hidden, tooltip or
    `title` attribute for the label) rather than removing navigation.
  - On narrow/mobile widths (e.g. `<640px`): collapse to an **off-canvas
    drawer** — sidebar translated off-screen by default, a hamburger
    trigger in a slim top bar toggles a `translateX(0)` open state with a
    backdrop overlay. Keep this achievable with a single boolean ref
    (`isSidebarOpen`) and CSS transitions — do not pull in a component
    library to do this.

## Design tokens

Introduce CSS custom properties, but **derive them from the values the
project already uses** — don't invent a new brand palette:

1. Search the global stylesheet (and view-level `<style scoped>` blocks) for
   hex colors: `grep -rnoE "#[0-9a-fA-F]{3,6}" client/src` (adjust the path
   to wherever source actually lives). Tally the distinct values and how
   often each occurs — the most-repeated grays/blues/status colors are the
   real palette.
2. Also collect the spacing values in use (`rem`/`px` values inside
   `padding`, `margin`, `gap`) to see what scale is already implied.
3. Add a `:root { ... }` block (in the global stylesheet you located
   earlier) mapping the *existing* values to named tokens, for example
   (illustrative only — substitute the values you actually found):

   ```css
   :root {
     /* Colors — extracted from existing usage, not invented */
     --color-text-primary: #0f172a;
     --color-text-secondary: #64748b;
     --color-border: #e2e8f0;
     --color-accent: #2563eb;
     --color-surface: #ffffff;
     --color-bg: #f8fafc;
     /* keep any existing status colors (success/warning/danger/info)
        found on badges, alerts, etc. */

     /* Spacing scale — 4px base, matching values already observed */
     --space-1: 0.25rem;  /* 4px */
     --space-2: 0.5rem;   /* 8px */
     --space-3: 0.75rem;  /* 12px */
     --space-4: 1rem;     /* 16px */
     --space-6: 1.5rem;   /* 24px */
     --space-8: 2rem;     /* 32px */

     --sidebar-width: 260px;
     --sidebar-width-collapsed: 64px;
   }
   ```

4. Replace hardcoded values with `var(--token)` **only in the code you are
   touching for this redesign** (sidebar, root layout, any view whose
   spacing you normalize per step 6 below). Do not do a repo-wide
   find-and-replace of every hex code as part of this change — that's a
   much bigger, separate refactor and risks scope creep/regressions.

## Implementation steps

Work in this order. If you determined a Vue specialist subagent exists,
delegate each `.vue` file creation/edit below to it with a scoped
instruction (don't dump the whole plan in one message — one focused
delegation per step keeps diffs reviewable). If no such subagent exists,
perform the edits directly.

1. **Introduce design tokens.** Add the `:root` custom-property block to
   the global stylesheet file located during analysis (additive only —
   don't remove existing rules yet).
2. **Build the Sidebar component.** Create a new component (e.g.
   `Sidebar.vue`, placed alongside the other shared layout components in
   this project's components directory) implementing the structure from
   "Sidebar design spec": brand header, `<nav>` with one link per route
   (reusing the existing icon approach and existing i18n/text-label
   pattern if the project has one), and a footer slot.
3. **Relocate right-pinned controls.** Move any component that was pinned
   to the end of the old horizontal bar (profile menu, language switcher,
   notification bell, etc.) into the sidebar footer. These are typically
   self-contained (own internal state/dropdown logic), so this should be a
   template relocation, not a rewrite of the component itself — confirm
   that assumption by reading the component first.
4. **Convert the root layout.** In the app root component: change the
   outer container from column-flex to row-flex, replace the old
   `<header class="top-nav">` block with `<Sidebar />`, and keep
   `<router-view>` (and any secondary toolbar rendered above it) inside the
   remaining content column. Preserve every existing route, modal, and
   top-level state/behavior in the root component untouched — this is a
   layout change, not a functional rewrite.
5. **Remove sticky-offset coupling.** For every element found in analysis
   step 5 (e.g. a filter bar hardcoded to `top: <old-nav-height>px`),
   change its offset to `top: 0` (it's now the first sticky element in its
   own column) or drop `position: sticky` if it no longer needs to persist
   on scroll. Re-verify it still visually sits flush at the top of the
   content column.
6. **Normalize per-view spacing/style overrides.** For each view-level
   style override identified in analysis step 7 that conflicts with (rather
   than intentionally extends) a global class, reconcile it: either remove
   the local override so the view inherits the now-tokenized global style,
   or, if the divergence was intentional, convert its hardcoded values to
   the same `var(--token)` values for consistency. Do this view-by-view,
   confirming each one still renders correctly before moving to the next.
7. **Sweep for remaining layout assumptions.** Grep for the old nav's
   height/class name (e.g. old `top-nav` class, its pixel height) across
   the codebase to catch any other component that assumed a horizontal bar
   above it (padding-top hacks, z-index stacking assumptions, etc.).

Throughout: **only change layout and visual design.** Every existing route,
every filter, every piece of business logic, every prop/emit contract
between components must keep working exactly as before. If you notice
unrelated issues (like a view with no route/nav entry, or dead code), flag
them to the user as an aside — do not fix them as part of this change
unless asked.

## Verification

1. **Static review.** If a read-only review subagent is available in this
   project (check `.claude/agents/` for one, e.g. named `code-reviewer`),
   run it against the diff before considering the change done.
2. **Visual check.** If Playwright MCP tools are available in this
   environment, use them to actually look at the result rather than just
   reading code:
   - Start (or confirm already running) the dev server for this project.
   - Navigate to each route the sidebar links to and take a screenshot at
     a desktop viewport (e.g. `1440x900`).
   - Resize to a tablet width (e.g. `900x800`) and confirm the sidebar
     collapses to the icon-only rail as designed.
   - Resize to a mobile width (e.g. `390x844`) and confirm the off-canvas
     drawer opens/closes correctly and doesn't overlap content when
     closed.
   - Confirm the active nav item highlights correctly on each route, and
     that any relocated controls (profile menu, language switcher, etc.)
     still open/function from their new position.
   - In this repo specifically, the frontend runs at `http://localhost:3000`
     — navigate there and through `/`, `/inventory`, `/orders`, `/demand`,
     `/spending`, `/reports`.
3. If either check surfaces a regression, fix it and re-run before
   reporting completion.

## Key Reminders

- Discover the project's Vue specialist subagent (via CLAUDE.md /
  `.claude/agents/`) before editing any `.vue` file — delegate to it if one
  exists, otherwise implement directly with Vue 3 best practices.
- Never hardcode this skill's own examples' file paths as if they were
  universal — always locate the real root layout component, nav markup,
  route list, and global stylesheet in the target project first.
- Extract design tokens (colors, spacing) from values already in use;
  don't invent a new palette.
- Remove sticky/fixed pixel-offset couplings that assumed a horizontal nav
  height — they become `top: 0` (or non-sticky) once the nav is vertical.
- Reconcile per-view style overrides that silently diverge from global
  classes as part of normalizing spacing, not just token substitution.
- Preserve every existing route, filter, and behavior — this is a layout
  and visual redesign only.
- Build responsive collapse (icon rail / off-canvas drawer) without adding
  a new UI/icon library unless the project already uses one.
- Review the diff (via a review subagent if present) and visually verify
  across breakpoints (Playwright MCP if available) before calling the work
  done.

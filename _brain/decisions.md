# Decisions: Tab Switcher Prototype

## Single index.html
**Decision:** Everything lives in one self-contained HTML file.
**Why:** Zero build step, opens directly in browser, easy to iterate and share.

## Group color auto-assignment: sequential unused-first
**Decision:** `pickGroupColorIdx()` walks indices 1–7 and returns the first not currently in use by any group; wraps to 1 when all taken.
**Why:** Predictable (same sequence every time), maximally distinct (reuses freed colors before picking occupied ones), matches how Chrome/Edge/Safari assign tab group colors.
**Rejected:** Random — could land on the same color as an existing group.

## Group creation starts expanded
**Decision:** `createNewGroup` sets `expanded: true`.
**Why:** The user just created the group — showing it collapsed and requiring a click to open it was an unnecessary extra step.

## No live rename in the group context menu
**Decision:** Group name only updates in the tabstrip on Enter, not on every keystroke.
**Why:** User explicitly reverted live-rename after it was briefly implemented.

## Context menu auto-opens on group creation
**Decision:** After `createNewGroup` calls `renderAll()`, a `requestAnimationFrame` finds the new group element and calls `showContextMenu(buildGroupMenu(groupId), ...)` positioned below it.
**Why:** Lets the user immediately name the group and confirm the color without a right-click.

## Expand animation: keep fixed width until cursor leaves
**Decision:** At `transitionend`, don't recompute flex-grow. Keep `flex: 0 0 auto; width: cappedEndW` frozen. `unfreezeTabs()` (on tabstrip mouseleave) handles restoring the group's natural `flex: N 1 0`.
**Why:** Any floating-point difference in the flex formula caused a 1–2px sub-tab width jump at the end of the animation. Keeping the element at a fixed pixel width eliminates the flash entirely.

## Collapse animation: transparent container + pixel overlay
**Decision:** Set `el.style.background = 'transparent'` (not the group color) and initialize the overlay with pixel dimensions (`width: startWpx; height: 38px`) instead of `100%/100%`.
**Why:** (1) With a colored container, the overlay's shrinking pill shape is invisible. (2) CSS can't interpolate `height: 100%` → `24px` (different units), so height would jump instead of animate.

## CSS .group-tab.expanded overrides require inline style
**Decision:** Always set `el.style.background = group.color` explicitly after clearing animation styles; never rely on CSS cascade.
**Why:** `.group-tab.expanded { background: var(--color-group) }` (green) wins whenever the inline style is cleared. Both `expandGroup` (at transitionend) and `collapseGroup` (setup) must use `group.color || 'transparent'` directly.

## Color picker: 18×18 hit area + 12×12 inner dot
**Decision:** Each swatch is a `.ctx-swatch` (18×18 flex container) wrapping a `.ctx-swatch-dot` (12×12 circle). Color is set on the dot, not the container.
**Why:** Matches Figma exactly (node 390:9280 — ellipse at x=3,y=3,w=12,h=12 inside 18×18 frame). The 3px gap between dot edge and container edge is where the selection ring sits via `outline: 1.5px solid; outline-offset: 1.5px`.

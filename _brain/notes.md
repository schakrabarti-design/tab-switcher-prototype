# Notes: Tab Switcher Prototype

## 2026-04-14 — Session summary: context menus, groups, animation polish

### Implemented
- Right-click context menus (6 variants) for tabs, sub-tabs, and group pills
- Shift+click multi-selection with `selectedTabIds` Set; first shift-click anchors from active tab
- Group color system: `GROUP_COLORS` array, `colorIdx` tracking, `applyGroupColor()` helper
- Auto-color assignment on group creation (`pickGroupColorIdx()`)
- Groups start expanded on creation
- Context menu (Menu 6) auto-opens on group creation with name pre-selected
- Group expand/collapse animation polish (see decisions for details)
- Matched Figma color picker exactly (18×18 + 12×12 dot structure, node 390:9280)

### Known CSS gotcha
`.group-tab.expanded { background: var(--color-group) }` always resets to green.
Must override with `el.style.background = group.color` inline after any animation cleanup.

### frozenState mechanics
- Set on `mouseenter` of a group element via `freezeLeftTabs(groupEl)` — captures pixel widths of all left-side tabs
- Cleared on `mouseleave` of the entire `#tabstrip` element via `unfreezeTabs()`
- `applyFrozenState()` re-applies frozen constraints after any DOM rebuild (e.g. `rebuildGroupElement`)
- During `expandGroup`, the animation target is capped at `available - M_right * 32px` to prevent overflow while frozen
- `unfreezeTabs` re-computes expanded group's flex via the strip-wide formula after clearing frozen constraints

### Figma references used this session
- Color picker row: staging.figma.com/design/VV0lEzfx975wMbMiIrfwAp node 390:9280
- Group color variables: `--ramp-pale_*-300` (swatch circle), `--ramp-pale_*-200` (group bg)

## 2026-04-14 — Idea #002 queued
Next feature to add: AI chat state indicator on tabs. Concept: use Figma's badged icon component (red dot = thinking, green checkmark = complete) + play a sound when AI chat finishes. Reference component: https://staging.figma.com/design/ycDPAaAih2bZY5LgXehVai/UI3-Icons?node-id=11665:4349

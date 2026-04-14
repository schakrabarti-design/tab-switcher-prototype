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

## Open Figma comments — page 320:12728 (tracked 2026-04-14)
Source: staging.figma.com/design/VV0lEzfx975wMbMiIrfwAp node 320:12728

| # | Author | Comment | Status |
|---|--------|---------|--------|
| 1 | Somayan | Add a grayscale color to the color picker palette | Open |
| 2 | Somayan | Consider contrast between group color and text in light/dark modes; consider high contrast settings | Open |
| 3 | Somayan | How to deselect when multiple tabs are selected? | Open |
| 3r | Somayan | How to handle dragging tabs when multiple are selected | Open |
| 4 | Mark | "Add to New Tab Group" (on sub-tab menu) should be renamed "Add to Tab Group" for consistency — Somayan agreed | Open |
| 5 | Mark | What is the focus state for keyboard users navigating the color picker? How does it differ from selected? | Open |
| 5r | Mark | Is there any hover indication on color swatches? | Open |
| 5r | Mark | Note: coded with 2px focus outline; override selected state with focus state when keyboard navigating | Open |
| 6 | Mark | FPL menus are always dark — color swatches that work in light mode may blend in / disappear in dark mode. May need an adjusting border or mode-respecting menu | Open |
| 6r | Mark | Light mode colors pop against the dark menu, but in dark mode the colors blend in — especially a problem for the selection state ring | Open |

### Actionable prototype TODOs from comments
- [ ] Add a grayscale swatch to `GROUP_COLORS`
- [ ] Rename "Add to New Tab Group" → "Add to Tab Group" in `buildSubTabMenu`
- [ ] Design + implement deselect behavior for multi-tab selection (e.g. click outside, Escape, click selected tab again)
- [ ] Design drag behavior for multi-selected tabs
- [ ] Color swatch hover state (currently no visual feedback)
- [ ] Color swatch keyboard focus state — 2px focus outline, distinct from selected ring
- [ ] Review swatch colors for dark mode contrast — light pastels may be too subtle against the always-dark FPL menu background; consider a thin border on each swatch

---

## 2026-04-14 — Future work backlog

### Tab hover card
On tab hover, show a hover card — preview of the tab content + metadata. Potentially surface live thinking/AI state for Figma Make tabs inline in the card (not just a badge on the tab itself). Pairs with Idea #002 (AI chat state indicator).

### Visual polish pass
Current prototype is functional but rough. Needs a design pass that incorporates the new color system, group styling, and animation decisions made in the session above. Should feel closer to actual Figma Make UI quality.

---

## 2026-04-14 — Idea #002 queued
Next feature to add: AI chat state indicator on tabs. Concept: use Figma's badged icon component (red dot = thinking, green checkmark = complete) + play a sound when AI chat finishes. Reference component: https://staging.figma.com/design/ycDPAaAih2bZY5LgXehVai/UI3-Icons?node-id=11665:4349

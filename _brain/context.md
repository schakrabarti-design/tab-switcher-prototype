# Project Context: Tab Switcher Prototype

## What
A browser-based prototype of a tab switcher UI — specifically exploring Figma Make tab interactions. Features tab groups with expand/collapse, drag-and-drop reordering, and a "last active" smart chip.

## Why
Exploring UI concepts for Figma Make tab management. Serves as a testbed for ideas related to how tabs behave, look, and communicate state in Figma Make.

## Who
Solo prototype — used to demo and develop ideas for Figma Make's tab UI. May be shown to Figma desktop native team colleagues.

## Data model
- `TABS`: array of tab objects `{ id, title, favicon, groupId, deactivated }`
- `GROUPS`: object keyed by groupId — `{ name, expanded, lastActive, color, colorIdx }`
- `INITIAL_GROUPS`: reset-state snapshot of GROUPS
- `GROUP_COLORS`: array of `{ swatch, bg }` — swatch = -300 ramp (circle fill), bg = -200 ramp (group background). Index 0 = none (null/null), indices 1–7 = red, violet, blue, purple, yellow, pink, persimmon
- `selectedTabIds`: Set of tab IDs shift-selected by the user
- `frozenState`: `{ groupId, widths[] }` — left tab widths frozen while a group is hovered, cleared on tabstrip mouseleave

## Key functions
- `freezeLeftTabs(groupEl)` / `unfreezeTabs()` — pin/release left tabs so expand only shifts right tabs
- `applyFrozenState()` — re-applies frozen constraints after DOM rebuild
- `expandGroup(groupId, pillEl)` / `collapseGroup(groupId, el)` — animated expand/collapse
- `rebuildGroupElement(groupId, oldEl)` — replace a group's DOM element in-place
- `applyGroupColor(groupId)` — apply group.color to the correct element (label when collapsed, container when expanded)
- `pickGroupColorIdx()` — returns lowest-index unused color (1–7), wraps to 1 when all taken
- `createNewGroup(tabIds)` — creates group with expanded:true + auto-color + auto-opens context menu
- `renderTabstrip()` / `renderAll()` — full re-render

## Right-click context menus (6 variants)
1. Regular tab, no groups — with "Add to Tab Group ▶ New Tab Group"
2. Regular tab, groups exist — submenu includes existing group names
3. Multi-selected tabs, no groups
4. Multi-selected tabs, groups exist
5. Sub-tab inside expanded group — "Remove from Tab Group", "Add to New Tab Group"
6. Group pill — name input (pre-focused, pre-selected) + color picker + Ungroup/Delete group

Menu 6 auto-opens on group creation, positioned below the group element.

## Active ideas being prototyped here
- Idea #002: AI chat state indicator (badge + sound on tab when AI chat completes)

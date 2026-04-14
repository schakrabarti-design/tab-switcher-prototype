# Open TODOs: Tab Switcher Prototype

## Features
- [ ] **Tab hover card** — preview of tab content + metadata on hover; surface live AI thinking state inline (pairs with Idea #002)
- [ ] **Idea #002: AI chat state badge + sound** — badge on tab (red dot = thinking, green checkmark = complete) + sound when AI chat finishes. Reference: staging.figma.com/design/ycDPAaAih2bZY5LgXehVai/UI3-Icons node 11665:4349
- [ ] **Visual polish pass** — design refresh to feel closer to actual Figma Make UI quality; incorporate new color system, group styling, and animation work

## Context menus & multi-selection
- [ ] **Deselect behavior for multi-tab selection** — click outside, Escape, or click a selected tab again (needs design decision)
- [ ] **Drag behavior for multi-selected tabs** (needs design decision)

## Color picker (from Figma comments, page 320:12728)
- [ ] **Add grayscale swatch** to `GROUP_COLORS`
- [ ] **Rename** "Add to New Tab Group" → "Add to Tab Group" in `buildSubTabMenu` (Mark comment #4, agreed)
- [ ] **Swatch hover state** — currently no visual feedback
- [ ] **Swatch keyboard focus state** — 2px outline distinct from selected ring; override selected with focus state while keyboard navigating (Mark's note: coded as 2px focus outline)
- [ ] **Dark mode swatch contrast** — light pastels blend into always-dark FPL menu; add thin border or consider mode-respecting approach (Mark comments #5r, #6, #6r)
- [ ] **Group color contrast in light/dark modes + high contrast settings** (Somayan comment #2)

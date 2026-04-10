# Prototype Controls Panel

**Date:** 2026-04-10
**Status:** Approved

## Overview

Replace the two existing floating control buttons with a consolidated settings panel accessible via a single ⚙ button. Add two new testing controls: a file type scenario toggle and a functional close (X) button with a reset.

## Panel UI

A single **⚙** button is fixed bottom-right, styled with the existing `.proto-control` dark-glass aesthetic. Clicking it toggles a compact floating panel directly above the button. The panel has three labeled sections:

### Scenarios
- **File type** row: an on/off toggle + a dropdown (Design, FigJam, Prototype, Slide, Make)
  - Toggle **off**: tabs render with their original mixed types (default)
  - Toggle **on**: all tabs switch to the selected type instantly
  - Changing the dropdown while the toggle is on immediately re-renders

### Tabs
- **Reset** button: restores `TABS` and `GROUPS` to their original hardcoded values, resets `activeTabIndex` to its default (2 / "Prototype"), and re-renders the tabstrip and switcher grid

### Appearance
- Subtab style toggle (moved from existing standalone button)
- Theme toggle (moved from existing standalone button)

## File Type Scenario

When the toggle is on, each tab's `iconUrl`, `colorUrl`, and `bg` are replaced with the values for the selected type. Tab names, group membership, active state, collaborators, and deactivated state are all preserved. Toggling off restores original values.

File type → icon/color/bg mapping:

| Type | iconUrl | colorUrl | bg |
|---|---|---|---|
| Design | ICON_DESIGN | ICON_PEN_COLOR | #EAF3FC |
| FigJam | ICON_FIGJAM | ICON_FIGJAM_COLOR | #F0E8FC |
| Prototype | ICON_PLAY | ICON_PLAY_COLOR | #E8FAF0 |
| Slide | ICON_SLIDE | ICON_SLIDE_COLOR | #FDF4E3 |
| Make | ICON_MAKE | ICON_MAKE_COLOR | #E8F8FD |

## Close Button

The X button on every tab (regular and sub-tab) becomes functional:

- Removes the tab from `TABS`
- If the closed tab was active, focus moves to the tab at `index - 1`, or index 0 if it was the first tab
- If a group drops to one member, the group is dissolved (member's `groupId` set to `null`, group deleted from `GROUPS`)
- If a group drops to zero members, the group entry is deleted
- Re-renders the tabstrip after removal

The X on the collapsed group pill removes only the displayed (lastActive) sub-tab, consistent with current behavior.

## Reset Button

Restores state to initial values:
- Deep-copies the original `TABS` array (hardcoded at page load)
- Restores `GROUPS` to `{ work: { name: 'Work', expanded: false, lastActive: null } }`
- Sets `activeTabIndex` to `2`
- Clears any active file type scenario (toggle off)
- Calls `renderAll()`

## What Does Not Change

- Tab names, collaborators, deactivated state — unaffected by file type scenario
- Drag-and-drop behavior
- Group expand/collapse animations
- Switcher overlay
- All existing keyboard shortcuts

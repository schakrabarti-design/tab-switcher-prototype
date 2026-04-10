# Prototype Controls Panel Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the two standalone floating control buttons with a consolidated ⚙ settings panel, add a file-type scenario toggle, make the regular-tab X button functional, and add a reset button.

**Architecture:** All changes are contained in `index.html`. New HTML goes just after the `.window` div. New CSS goes in the existing `<style>` block. New JS goes just before the `// ── Init` comment at the bottom of the `<script>` block. Existing toggle IDs (`subtabStyleToggle`, `themeToggle`) are preserved so their JS handlers need no changes.

**Tech Stack:** Vanilla HTML/CSS/JS, no build step.

---

## Files

- Modify: `index.html`
  - HTML ~line 824: replace two standalone `<button>` elements with gear button + panel
  - CSS ~line 725: replace `.proto-control` / `#subtabStyleToggle` / `#themeToggle` rules with panel styles
  - JS ~line 1740: add file-type scenario state + handlers, close handler wiring, reset handler

---

### Task 1: Panel HTML + CSS scaffold

**Files:**
- Modify: `index.html` (HTML ~line 824, CSS ~line 725)

- [ ] **Step 1: Replace the two standalone buttons with panel HTML**

Find and remove these two lines (~line 824):
```html
<button id="subtabStyleToggle" class="proto-control"></button>
<button id="themeToggle" class="proto-control"></button>
```

Replace with:
```html
<button id="settingsBtn" class="proto-settings-btn" title="Settings">⚙</button>

<div id="settingsPanel" class="proto-settings-panel" hidden>

  <div class="proto-section">
    <div class="proto-section-label">Scenarios</div>
    <div class="proto-row">
      <span class="proto-row-label">File type</span>
      <button id="fileTypeToggle" class="proto-pill-btn">Off</button>
      <select id="fileTypeSelect" class="proto-select">
        <option value="design">Design</option>
        <option value="figjam">FigJam</option>
        <option value="prototype">Prototype</option>
        <option value="slide">Slide</option>
        <option value="make">Make</option>
      </select>
    </div>
  </div>

  <div class="proto-section">
    <div class="proto-section-label">Tabs</div>
    <button id="resetBtn" class="proto-action-btn">↺ Reset tabs</button>
  </div>

  <div class="proto-section">
    <div class="proto-section-label">Appearance</div>
    <button id="subtabStyleToggle" class="proto-action-btn"></button>
    <button id="themeToggle" class="proto-action-btn"></button>
  </div>

</div>
```

- [ ] **Step 2: Replace the old `.proto-control` CSS rules with panel styles**

Find and remove the entire `/* ── Controls ──` block (~line 725–747):
```css
  /* ── Controls ─────────────────────────────────────────────── */
  .proto-control { ... }
  .proto-control:hover { ... }
  #subtabStyleToggle { bottom: 60px; }
  #themeToggle { bottom: 20px; }
```

Replace with:
```css
  /* ── Settings panel ───────────────────────────────────────── */
  .proto-settings-btn {
    position: fixed;
    right: 20px;
    bottom: 20px;
    width: 36px;
    height: 36px;
    border-radius: 10px;
    background: rgba(0,0,0,0.35);
    border: 1px solid rgba(255,255,255,0.15);
    font-size: 16px;
    color: rgba(255,255,255,0.8);
    cursor: pointer;
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    z-index: 999;
    transition: background 0.15s;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .proto-settings-btn:hover,
  .proto-settings-btn.open { background: rgba(0,0,0,0.55); }

  .proto-settings-panel {
    position: fixed;
    right: 20px;
    bottom: 64px;
    width: 220px;
    background: rgba(30,30,30,0.92);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 12px;
    padding: 8px 0;
    z-index: 999;
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
    font-family: var(--font);
    box-shadow: 0 8px 32px rgba(0,0,0,0.4);
  }

  .proto-section { padding: 6px 12px; }
  .proto-section + .proto-section {
    border-top: 1px solid rgba(255,255,255,0.08);
    margin-top: 4px;
    padding-top: 10px;
  }

  .proto-section-label {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    color: rgba(255,255,255,0.35);
    margin-bottom: 6px;
  }

  .proto-row {
    display: flex;
    align-items: center;
    gap: 6px;
    margin-bottom: 4px;
  }
  .proto-row-label {
    font-size: 12px;
    color: rgba(255,255,255,0.7);
    flex: 1;
  }

  .proto-pill-btn {
    font-size: 11px;
    font-family: var(--font);
    padding: 2px 8px;
    border-radius: 20px;
    border: 1px solid rgba(255,255,255,0.2);
    background: transparent;
    color: rgba(255,255,255,0.5);
    cursor: pointer;
    transition: background 0.12s, color 0.12s;
    white-space: nowrap;
  }
  .proto-pill-btn.active {
    background: rgba(255,255,255,0.15);
    color: rgba(255,255,255,0.9);
    border-color: rgba(255,255,255,0.35);
  }

  .proto-select {
    font-size: 11px;
    font-family: var(--font);
    background: rgba(255,255,255,0.08);
    border: 1px solid rgba(255,255,255,0.15);
    border-radius: 6px;
    color: rgba(255,255,255,0.7);
    padding: 2px 4px;
    cursor: pointer;
    max-width: 80px;
  }

  .proto-action-btn {
    display: block;
    width: 100%;
    text-align: left;
    font-size: 12px;
    font-family: var(--font);
    padding: 5px 6px;
    border-radius: 6px;
    border: none;
    background: transparent;
    color: rgba(255,255,255,0.7);
    cursor: pointer;
    transition: background 0.12s;
    margin-bottom: 2px;
  }
  .proto-action-btn:hover { background: rgba(255,255,255,0.1); }
```

- [ ] **Step 3: Verify visually**

Open `index.html` in a browser. You should see a ⚙ button in the bottom-right. Clicking it does nothing yet. The old two buttons should be gone.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Add settings panel scaffold (HTML + CSS)"
```

---

### Task 2: Wire the gear button and move existing toggles into the panel

**Files:**
- Modify: `index.html` (JS ~line 1713)

- [ ] **Step 1: Add gear button open/close logic**

Find the `// ── Sub-tab style toggle` comment (~line 1713). Insert the following block immediately before it:

```javascript
  // ── Settings panel ────────────────────────────────────────
  const settingsBtn   = document.getElementById('settingsBtn');
  const settingsPanel = document.getElementById('settingsPanel');

  settingsBtn.addEventListener('click', () => {
    const isOpen = !settingsPanel.hidden;
    settingsPanel.hidden = isOpen;
    settingsBtn.classList.toggle('open', !isOpen);
  });

  document.addEventListener('click', (e) => {
    if (!settingsPanel.hidden &&
        !settingsPanel.contains(e.target) &&
        e.target !== settingsBtn) {
      settingsPanel.hidden = true;
      settingsBtn.classList.remove('open');
    }
  });

```

- [ ] **Step 2: Update existing subtab + theme toggle button text init**

The existing `syncSubtabToggle()` and `syncToggle()` functions set `textContent` on the buttons — they still reference the same element IDs, so they continue to work unchanged. No code changes needed here; just verify.

- [ ] **Step 3: Verify**

Reload. Clicking ⚙ opens the panel; clicking elsewhere closes it. The Appearance section shows the subtab-style and theme buttons with their correct labels, and they still work.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Wire settings gear button open/close and existing toggles"
```

---

### Task 3: File type scenario toggle

**Files:**
- Modify: `index.html` (JS — near the top of `<script>`, and near the settings panel section)

- [ ] **Step 1: Store original tab types and define the type map**

Find the line `let activeTabIndex = 2;` (~line 866). Insert after it:

```javascript
  // Original tab type data — captured before any scenario is applied
  const ORIGINAL_TYPES = TABS.map(t => ({ iconUrl: t.iconUrl, colorUrl: t.colorUrl, bg: t.bg }));

  // Scenario state
  let fileTypeScenarioActive = false;

  // File type → icon/color/bg mapping
  const FILE_TYPE_MAP = {
    design:    { iconUrl: ICON_DESIGN, colorUrl: ICON_PEN_COLOR,    bg: '#EAF3FC' },
    figjam:    { iconUrl: ICON_FIGJAM, colorUrl: ICON_FIGJAM_COLOR, bg: '#F0E8FC' },
    prototype: { iconUrl: ICON_PLAY,   colorUrl: ICON_PLAY_COLOR,   bg: '#E8FAF0' },
    slide:     { iconUrl: ICON_SLIDE,  colorUrl: ICON_SLIDE_COLOR,  bg: '#FDF4E3' },
    make:      { iconUrl: ICON_MAKE,   colorUrl: ICON_MAKE_COLOR,   bg: '#E8F8FD' },
  };

  function applyFileTypeScenario(type) {
    const map = FILE_TYPE_MAP[type];
    TABS.forEach(t => {
      t.iconUrl  = map.iconUrl;
      t.colorUrl = map.colorUrl;
      t.bg       = map.bg;
    });
    renderAll();
  }

  function clearFileTypeScenario() {
    TABS.forEach((t, i) => {
      t.iconUrl  = ORIGINAL_TYPES[i].iconUrl;
      t.colorUrl = ORIGINAL_TYPES[i].colorUrl;
      t.bg       = ORIGINAL_TYPES[i].bg;
    });
    renderAll();
  }
```

- [ ] **Step 2: Wire the toggle button and dropdown**

In the settings panel JS block (after the gear open/close logic you added in Task 2), add:

```javascript
  // ── File type scenario ────────────────────────────────────
  const fileTypeToggle = document.getElementById('fileTypeToggle');
  const fileTypeSelect = document.getElementById('fileTypeSelect');

  function syncFileTypeToggle() {
    fileTypeToggle.textContent = fileTypeScenarioActive ? 'On' : 'Off';
    fileTypeToggle.classList.toggle('active', fileTypeScenarioActive);
    fileTypeSelect.disabled = !fileTypeScenarioActive;
    fileTypeSelect.style.opacity = fileTypeScenarioActive ? '1' : '0.4';
  }

  fileTypeToggle.addEventListener('click', () => {
    fileTypeScenarioActive = !fileTypeScenarioActive;
    if (fileTypeScenarioActive) {
      applyFileTypeScenario(fileTypeSelect.value);
    } else {
      clearFileTypeScenario();
    }
    syncFileTypeToggle();
  });

  fileTypeSelect.addEventListener('change', () => {
    if (fileTypeScenarioActive) applyFileTypeScenario(fileTypeSelect.value);
  });

  syncFileTypeToggle();
```

- [ ] **Step 3: Verify**

Reload. Open the panel. The File type row shows "Off" (dimmed dropdown). Toggle it on → all tabs switch to Design icons and blue backgrounds. Change the dropdown to FigJam → all tabs switch to FigJam style. Toggle off → original mixed types restored. The group tab and its sub-tabs also update.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Add file type scenario toggle to settings panel"
```

---

### Task 4: Functional close button on regular tabs

**Files:**
- Modify: `index.html` (JS `renderTabstrip` function ~line 910)

- [ ] **Step 1: Add a `closeTab` helper function**

Find the `function setActiveTab(index)` function (~line 1617). Insert a `closeTab` function immediately before it:

```javascript
  function closeTab(index) {
    const tab = TABS[index];

    // If tab is in a group, handle group dissolution
    if (tab.groupId) {
      const remaining = TABS.filter((t, i) => t.groupId === tab.groupId && i !== index);
      if (remaining.length === 0) {
        delete GROUPS[tab.groupId];
      } else if (remaining.length === 1) {
        remaining[0].groupId = null;
        delete GROUPS[tab.groupId];
      }
    }

    TABS.splice(index, 1);

    // Restore original types entry to stay in sync
    ORIGINAL_TYPES.splice(index, 1);

    // Adjust active index
    if (TABS.length === 0) {
      activeTabIndex = 0;
    } else if (activeTabIndex >= TABS.length) {
      activeTabIndex = TABS.length - 1;
    } else if (index < activeTabIndex) {
      activeTabIndex--;
    }

    renderAll();
  }
```

- [ ] **Step 2: Wire the click handler in `renderTabstrip`**

Find the regular tab rendering block in `renderTabstrip`. It currently looks like (~line 1016–1026):

```javascript
      el.addEventListener('click', () => setActiveTab(i));
      attachDragHandlers(el, 'tab', i, null);
      list.appendChild(el);
```

Replace with:

```javascript
      el.addEventListener('click', (e) => {
        if (e.target.closest('.tab-close')) return;
        setActiveTab(i);
      });
      el.querySelector('.tab-close').addEventListener('click', (e) => {
        e.stopPropagation();
        closeTab(i);
      });
      attachDragHandlers(el, 'tab', i, null);
      list.appendChild(el);
```

- [ ] **Step 3: Verify**

Reload. Hover a tab — the X appears. Click it — the tab disappears and an adjacent tab becomes active. Close the active tab — focus moves left. Close a grouped tab from the group pill's chip — the chip updates (existing behavior, should still work). Close tabs until one remains — still works, no errors.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Make regular tab close button functional"
```

---

### Task 5: Reset button

**Files:**
- Modify: `index.html` (JS — settings panel section)

- [ ] **Step 1: Store the initial TABS and GROUPS for reset**

Find the `ORIGINAL_TYPES` declaration you added in Task 3. Directly after it, add:

```javascript
  // Deep copies of initial state for reset
  const INITIAL_TABS   = TABS.map(t => ({ ...t }));
  const INITIAL_GROUPS = { work: { name: 'Work', expanded: false, lastActive: null } };
```

- [ ] **Step 2: Wire the reset button**

In the settings panel JS block (after the file type scenario handlers), add:

```javascript
  // ── Reset ─────────────────────────────────────────────────
  document.getElementById('resetBtn').addEventListener('click', () => {
    // Restore tabs
    TABS.length = 0;
    INITIAL_TABS.forEach(t => TABS.push({ ...t }));

    // Restore original types in sync
    ORIGINAL_TYPES.length = 0;
    INITIAL_TABS.forEach(t => ORIGINAL_TYPES.push({ iconUrl: t.iconUrl, colorUrl: t.colorUrl, bg: t.bg }));

    // Restore groups
    Object.keys(GROUPS).forEach(k => delete GROUPS[k]);
    Object.keys(INITIAL_GROUPS).forEach(k => { GROUPS[k] = { ...INITIAL_GROUPS[k] }; });

    // Clear scenario
    fileTypeScenarioActive = false;
    syncFileTypeToggle();

    // Reset active index and re-render
    activeTabIndex = 2;
    renderAll();
  });
```

- [ ] **Step 3: Verify**

Reload. Close several tabs and change the file type scenario to FigJam. Open the panel and click "↺ Reset tabs" — all original tabs reappear, the file type scenario is cleared (toggle shows "Off"), and the active tab is "Prototype" (index 2). The Work group is collapsed and intact.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Add reset button to restore initial tab state"
```

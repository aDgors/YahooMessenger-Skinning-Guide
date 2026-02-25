# Yahoo! Messenger Skin Template Plan

> A comprehensive guide & reusable boilerplate for creating custom skins

**Author:** KassRo
**Created:** February 2026
**Based on:** Default, Ruby Red - Teto, Icy Blue - Hatsune Miku and more.

> **Note:** This documentation is a living document — it is progressively expanded and refined as new skins are being developed. Expect new sections, tips, and corrections to appear over time as more edge cases and techniques are discovered through hands-on skin work.

This template uses the Ruby Red - Teto approach (full `<style>` redefinitions in theme.xml) for maximum control. It covers the essential components: colors, theme, FriendList, and IMWindow.

---

## Table of Contents

1. [Overview & Quick Start](#1-overview--quick-start)
2. [Folder Structure](#2-folder-structure)
3. [How the Skinning System Works](#3-how-the-skinning-system-works)
4. [colors.xml Reference (Grouped by Component)](#4-colorsxml-reference-grouped-by-component)
5. [theme.xml Reference (Window Frames & Controls)](#5-themexml-reference-window-frames--controls)
6. [FriendList.xml Reference](#6-friendlistxml-reference)
7. [IMWindow.xml Reference](#7-imwindowxml-reference)
8. [Image Assets Guide (Grouped Summary)](#8-image-assets-guide-grouped-summary)
9. [The resizemargin System (Artwork Placement)](#9-the-resizemargin-system-artwork-placement)
10. [Dark vs Light Theme Guide](#10-dark-vs-light-theme-guide)
11. [Step-by-Step: Creating a New Skin](#11-step-by-step-creating-a-new-skin)
12. [Troubleshooting](#12-troubleshooting)
13. [Files Reference Table](#13-files-reference-table)

---

## 1. Overview & Quick Start

Yahoo! Messenger skins work by **overriding** the Default skin. You only need to include files that differ from Default — anything missing falls back to the Default skin automatically.

### Quick Start

1. Copy the "Skin Template" folder
2. Rename it to your skin name
3. Edit `Localized.xml` — set your display name
4. Edit `colors.xml` — set your color palette
5. Replace/create images in `theme/`, `FriendList/`, `IMWindow/`
6. Edit `theme/theme.xml` — configure window frames
7. Edit `FriendList/FriendList.xml` and `IMWindow/IMWindow.xml` as needed
8. Place your skin folder in the Yahoo! Messenger skins directory
9. Restart Yahoo! Messenger and select your skin

### Minimum Viable Skin (just recoloring)

`colors.xml` + `Localized.xml` only — everything else falls back to Default.

---

## 2. Folder Structure

```
[Your Skin Name]/
│
├── colors.xml                Core color palette (REQUIRED)
├── Localized.xml             Display name shown in skin picker (REQUIRED)
├── preview.png               Skin preview thumbnail (recommended, ~150x200)
│
├── theme/
│   ├── theme.xml             Window frame & control styling
│   ├── framewindow.png       IM/chat window frame
│   ├── mainframewindow.png   Main buddy list window frame
│   ├── toolwindow.png        Popup/tool window frame
│   ├── framewindow.rgn       Window region shape (copy from base skin)
│   ├── mainframewindow.rgn   Window region shape (copy from base skin)
│   ├── toolwindow.rgn        Window region shape (copy from base skin)
│   ├── Yahoo!_Messenger.png  Logo in title bar (~161x34)
│   ├── menubar.png           Menu bar background
│   ├── menuitem.png          Menu dropdown item background
│   ├── pushbuttons.png       Standard button states
│   ├── headerbg.png          List header background
│   ├── slotborder.png        Plugin slot border
│   ├── slotborder_we_left.png   Slot border left edge
│   ├── slotborder_we_right.png  Slot border right edge
│   └── (other control images as needed)
│
├── images/
│   ├── bg_hover.png          List item hover background
│   ├── bg_selected.png       List item selected background
│   ├── btn-single-*.png      Various button images
│   ├── resize_ctrl.png       Window resize grip
│   └── (other UI element images)
│
├── FriendList/
│   ├── FriendList.xml        Buddy list layout overrides
│   ├── me_bg.png             "Me" panel background
│   ├── SearchBar.png         Search bar background (normal)
│   ├── SearchBar_h.png       Search bar background (hover)
│   ├── SearchBar_fc.png      Search bar background (focused)
│   ├── btn-single-status.png Status button image
│   ├── DisplayImgOverlay.png Avatar overlay
│   └── (optional: AddBuddyPanel.png, BottomPluginEdge.png, etc.)
│
├── IMWindow/
│   ├── IMWindow.xml          IM window layout overrides
│   ├── toolbar_back.png      IM toolbar background
│   └── (optional: toolbar button images, background art)
│
└── ContactCard/              (optional — images only, no XML needed)
    ├── background.png        Contact card background
    ├── Frame.png             Display image frame
    ├── CallButton.png        Call button image
    ├── card-CallButton.png   Card call button
    ├── SmsButton.png         SMS button image
    └── card-SmsButton.png    Card SMS button
```

> **Note:** `.rgn` files define window region shapes. Copy these from your base skin (e.g., Icy Blue or Ruby Red). They don't need modification for most skins.

---

## 3. How the Skinning System Works

### Inheritance

- Every skin inherits from Default automatically
- You only override what you want to change
- Missing files/values → Default is used

### XML Override Mechanisms

#### Method 1: `<style>` redefinition (used by Ruby Red - Teto)

```xml
<style id="framewindow" imagefile="framewindow.png"
    resizemargin="3,28,2,4"
    active_font="defaultfontbold" .../>
```

Replaces the **entire** style definition. Must include ALL attributes or they revert to defaults. Gives maximum control.

#### Method 2: `<override>` tag (used by Icy Blue - Miku)

```xml
<override id="mainframewindow" imagefile="mainframewindow.png"
    resizemargin="4,24,4,24"/>
```

Merges with the existing Default definition. Only the specified attributes are changed. Simpler but less explicit.

#### Method 3: `<override>` in section XML (FriendList.xml, IMWindow.xml)

```xml
<section id="IMWindow">
  <override id="IMWnd.ToolbarArea"
      imagefile="toolbar_back.png" resizemargin="0,0,144,0"/>
</section>
```

Overrides specific UI element properties within a section. Used in `FriendList.xml` and `IMWindow.xml`.

> **This template** uses Method 1 for `theme.xml` (maximum control) and Method 3 for `FriendList.xml`/`IMWindow.xml` (targeted overrides).

### Image Fallback

Images referenced in XML are searched in this order:

1. The current skin's subfolder (e.g., `theme/`, `images/`)
2. The Default skin's matching subfolder

If an image isn't found, it may render as blank/transparent.

---

## 4. colors.xml Reference (Grouped by Component)

`colors.xml` defines the entire color palette for your skin. Colors use hex RGB format (e.g., `"ff0000"` for red) or decimal R,G,B (e.g., `"255,0,0"`).

### Window Frame & Chrome

| Color ID | Description | Dark (Ruby Red) | Light (Icy Blue) |
|---|---|---|---|
| `mainwndbackcolor` | Main window background fill | `133,3,35` (ruby) | `219,231,245` (icy blue) |
| `activemaincaption` | Main window title text (active) | `f3f3f3` | `FFFFFF` |
| `itactivemaincaption` | Main window title text (inactive) | `999999` | `7a8180` |
| `activecaption` | Frame window title text (active) | `f3f3f3` | `FFFFFF` |
| `itactivecaption` | Frame window title text (inactive) | `999999` | `CCCCCC` |
| `activetoolcaption` | Tool window title text (active) | `333333` | `333333` |
| `itactivetoolcaption` | Tool window title text (inactive) | `cdcdcd` | `cdcdcd` |
| `windowframe` | Window frame accent color | `94,114,139` | `94,114,139` |

### Text & Content

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `text` | Default body text | `f3f3f3` | `333333` |
| `text_disabled` | Disabled/grayed text | `999999` | `999999` |
| `text_hyperlink` | System-generated hyperlinks | `ffffff` | `000000` |
| `text_user_hyperlink` | User-generated hyperlinks (status links) | `576cd7` | `576cd7` |
| `text_vitality` | Network-generated links (Buzz, etc.) | `41aee5` | `41aee5` |
| `text_sys_notif` | System notification text | `f3f3f3` | `000000` |
| `text_status_default` | Status message default color | `cccccc` | `999999` |
| `tooltip` | Tooltip background color | `ffffe1` | `ffffe1` |
| `tooltip_text` | Tooltip text color | `333333` | `000000` |

### Buttons

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `buttontext` | Button label text (normal) | `f3f3f3` | `333333` |
| `buttontext_h` | Button label text (hover) | `f3f3f3` | `51,51,51` |
| `buttontext_p` | Button label text (pressed) | `f3f3f3` | `0` |
| `buttontext_d` | Button label text (disabled) | `999999` | `999999` |

### Menus

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `mainmenubartext` | Main menu bar text (normal) | `dcdcdc` | `dcdcdc` |
| `mainmenubartext_h` | Main menu bar text (hover) | `f3f3f3` | `FFFFFF` |
| `mainmenubartext_d` | Main menu bar text (disabled) | `888888` | `888888` |
| `mainmenubartext_p` | Main menu bar text (pressed) | `f3f3f3` | `FFFFFF` |
| `menubartext` | Sub-menu bar text (normal) | `333333` | `333333` |
| `menubartext_h` | Sub-menu bar text (hover) | `FFFFFF` | `FFFFFF` |
| `menubartext_d` | Sub-menu bar text (disabled) | `888888` | `888888` |
| `menubartext_p` | Sub-menu bar text (pressed) | `333333` | `333333` |
| `menuitemtext` | Dropdown menu item (normal) | `333333` | `333333` |
| `menuitemtext_h` | Dropdown menu item (hover/selected) | `f3f3f3` | `FFFFFF` |
| `menuitemtext_d` | Dropdown menu item (disabled) | `888888` | `888888` |
| `menuitemtext_p` | Dropdown menu item (pressed) | `333333` | `333333` |

### Selection & Focus

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `selected_bg` | Selection/highlight background | `9d4a5e` (pink-red) | `496992` (steel blue) |
| `selected_text` | Selection text color | `f3f3f3` | `ffffff` |
| `ctrl_bg` | Control background (text fields, lists) | `ffffff` | `ffffff` |
| `ctrl_focused_bg` | Control background when focused | `9d4a5e` | `7695bf` |

### Buddy List Specific

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `groupheader` | Group header text/accent | `9d4a5e` | `496992` |
| `groupheader_text` | Group header label color | `f3f3f3` | `ffffff` |

### Toolbar & Bars

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `toolbar_border` | Toolbar border/separator color | `133,3,35` | `219,231,245` |

### Slot Manager

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `slotborder_gap` | Gap color between plugin slots | `4a0000` (dark maroon) | `7583be` (periwinkle) |

### System Colors (`syscolors` block)

These override Windows system colors **within** the messenger window. They control how standard Windows controls render (dialogs, lists, etc.).

> **Important:** All `syscolors` values MUST use decimal `R,G,B` format.
> Many syscolors should match your `mainwndbackcolor` for consistency.

| Color ID | Description | Dark | Light |
|---|---|---|---|
| `COLOR_WINDOW` | Window/control background | `133,3,35` | `219,231,245` |
| `COLOR_WINDOWTEXT` | Window text | `243,243,243` | `3,3,3` |
| `COLOR_BTNFACE` | Button/dialog background | `133,3,35` | `219,231,245` |
| `COLOR_BTNTEXT` | Button text | `243,243,243` | `51,51,51` |
| `COLOR_HIGHLIGHT` | Selection highlight | `157,74,94` | `73,105,146` |
| `COLOR_HIGHLIGHTTEXT` | Selected text | `243,243,243` | `255,255,255` |
| `COLOR_GRAYTEXT` | Disabled text | `153,153,153` | `153,153,153` |
| `COLOR_SCROLLBAR` | Scrollbar track color | `133,3,35` | `219,231,245` |
| `COLOR_3DFACE` | 3D element face | `133,3,35` | `219,231,245` |
| `COLOR_3DSHADOW` | 3D element shadow | `133,3,35` | `219,231,245` |

---

## 5. theme.xml Reference (Window Frames & Controls)

`theme/theme.xml` controls window frames, title bars, buttons, menus, scrollbars, and other standard UI controls.

### Window Frames

Three frame styles exist:

| Style ID | Description |
|---|---|
| `mainframewindow` | The main buddy list window |
| `framewindow` | IM/chat windows and secondary windows |
| `toolwindow` | Small popups, tooltips, tool dialogs |

Each frame style defines:

| Attribute | Description |
|---|---|
| `imagefile` | The frame border image (PNG) |
| `resizemargin` | Border widths: `"left, top, right, bottom"` |
| `resizemargin_*` | Per-edge stretch control (see [Section 9](#9-the-resizemargin-system-artwork-placement)) |
| `active_font` | Title bar font when window is focused |
| `active_textcolor` | Title text color when focused |
| `inactive_font` | Title bar font when not focused |
| `inactive_textcolor` | Title text color when not focused |
| `icon_xpos` / `icon_ypos` | Window icon position in title bar |
| `title_xpos` / `title_ypos` | Title text position (`-100,-100` = hidden) |

### Window Buttons

| Style ID | Image | Description |
|---|---|---|
| `closebutton` | `close.png` | Window close (X) |
| `maximizebutton` | `max.png` | Maximize |
| `restorebutton` | `restore.png` | Restore from maximize |
| `minimizebutton` | `min.png` | Minimize |
| `helpbutton` | `Marble.png` | Help (marble icon) |
| `tool_closebutton` | `tool_close.bmp` | Tool window close |

These are multi-state sprite images (normal/hover/pressed/disabled). If you don't include them, Default's buttons are used.

### Menu

The menu style controls:

| Attribute | Description | Default Image |
|---|---|---|
| `imagefile` | Menu dropdown background | `menu_bg.png` |
| `imagefile_menuitem` | Individual item background | `menuitem.png` |
| `imagefile_mainbaritem` | Main menu bar item | `mainmenubar.png` |
| `imagefile_baritem` | Sub-menu bar item | `menubar.png` |
| `resizemargin_baritem` | Stretch control for bar item | (see [Section 9](#9-the-resizemargin-system-artwork-placement)) |

### Controls (usually inherited from Default)

| Style ID | Description | Image(s) |
|---|---|---|
| `pushbutton` | Standard buttons | `pushbuttons.png` — 50x132 sprite |
| `radio` | Radio buttons | `radio.png` or `radio.bmp` |
| `checkbox` | Checkboxes | `checkbox.bmp` |
| `combobox` | Dropdown/combo boxes | `combo_back.bmp` + `combo_arrow.bmp` |
| `scrollbar_v` | Vertical scrollbar | `scroll_vbg/varrows/v_grip/thumb.png` |
| `scrollbar_h` | Horizontal scrollbar | `scroll_hbg/harrows/h_grip/thumb_h.png` |
| `tabctrl` | Tab control | `tabs.png` |
| `progressbar` | Progress bar | `progressbar.bmp` |
| `headerctrl` | List header | `headerctrl.bmp` |
| `groupbox` | Group box frame | `groupbox.png` |
| `tooltip` | Tooltip popup | (uses color definitions) |

Most of these can be left to Default. Override only what you want to visually match your color scheme.

---

## 6. FriendList.xml Reference

`FriendList/FriendList.xml` controls the main buddy list window layout. Most skins only need to override a few properties.

### Common Overrides

**MePanel** — The "me" section at the top of the buddy list

| Property | Description |
|---|---|
| `dock="top"` | Always docked to top |
| `resizemargin` | Controls artwork area: `"2,59,WIDTH,1"` where `WIDTH` = your artwork px |

**MePanel background image** (`me_bg.png`) is the background of the user's profile section. Typical size: ~169x61.

**SearchBar images:**

| File | Description | Typical Size |
|---|---|---|
| `SearchBar.png` | Normal state | ~70x32 |
| `SearchBar_h.png` | Hover state | ~70x32 |
| `SearchBar_fc.png` | Focused/active state | ~70x32 |

### MePanel resizemargin for Character Skins

```
resizemargin="2,59,160,1"
```

| Value | Meaning |
|---|---|
| `2` | 2px left fixed |
| `59` | 59px top fixed |
| `160` | **160px right fixed (YOUR ARTWORK SPACE)** |
| `1` | 1px bottom fixed |

The right value (160px in this example) defines how much space is reserved for your character artwork in the Me panel.

---

## 7. IMWindow.xml Reference

`IMWindow/IMWindow.xml` controls the IM conversation window layout. Typically only a few overrides are needed.

### Common Overrides

| Override ID | Property | Description |
|---|---|---|
| `IMWnd.MainToolbarArea` | `margin="7,0,0,0"` | Removes the default 2px top gap (Default is `"7,2,0,0"`) |
| `IMWnd.ToolbarArea` | `imagefile` | Your custom toolbar background (`toolbar_back.png`) |
| `IMWnd.ToolbarArea` | `resizemargin` | Artwork area: `"0,0,WIDTH,0"` — `WIDTH` = px reserved on right |
| `IMWnd.StatusBarTag` | `alpha="69"` | Status bar tag transparency |
| `IMWnd.StatusBar` | `alpha="107"` | Status bar text transparency |
| `IMWnd.StatusBarUrl` | `alpha="107"` | Status bar URL transparency |
| `IMWnd.StatusBarUrl2` | `alpha="107"` | Status bar second URL transparency |

> Alpha: `0` = invisible, `255` = fully visible. Lower values make status bar text more subtle.

### Toolbar Background (`toolbar_back.png`)

| Property | Description |
|---|---|
| Typical dimensions | ~200x44 |
| Ruby Red - Teto | 205x44 with 144px artwork area |
| Icy Blue - Miku | 200x44 with 190px artwork area |
| `resizemargin` right value | Controls how much is fixed (artwork); remaining left tiles |

---

## 8. Image Assets Guide (Grouped Summary)

Images are organized by category. Only override what you need — everything else falls back to Default.

### Window Frames

> **Location:** `theme/` · **Format:** PNG (32-bit with alpha transparency)

| File | Description | Reference Dimensions |
|---|---|---|
| `framewindow.png` | IM window frame border. The `resizemargin` in theme.xml slices this image. | 190x192 (Teto), 190x96 (Miku) |
| `mainframewindow.png` | Main buddy list window frame. Same slicing concept. | 190x128 (both skins) |
| `toolwindow.png` | Popup/tool window frame | 97x74 (both skins) |
| `Yahoo!_Messenger.png` | Logo displayed in the main window title bar | 161x34 |

### Menu & Toolbar

> **Location:** `theme/` and `IMWindow/` · **Format:** PNG

| File | Location | Description | Reference Dimensions |
|---|---|---|---|
| `menubar.png` | `theme/` | Menu bar background (height = multiple states stacked vertically) | 143x80 (Teto), 135x80 (Miku) |
| `menuitem.png` | `theme/` | Dropdown menu item hover/selected background | — |
| `menu_bg.png` | `theme/` | Menu dropdown panel background | — |
| `mainmenubar.png` | `theme/` | Main menu bar item background (Default only) | — |
| `toolbar_back.png` | `IMWindow/` | IM window toolbar background (right portion = character artwork area) | 205x44 (Teto), 200x44 (Miku) |

### Buttons

> **Location:** `theme/` and `images/` · **Format:** PNG (multi-state sprite sheets)

**`theme/pushbuttons.png`** — Standard push buttons (normal/hover/pressed/disabled). Ref: 50x132 (6 states stacked × 22px each).

**`images/` button files:**

| File | Description |
|---|---|
| `btn-single-small.png` | Small single button |
| `btn-single-small_tr.png` | Small transparent button |
| `btn-single-toolbar.png` | Toolbar button |
| `btn-single-dropdown.png` | Dropdown button |
| `btn-single-dropdown-small.png` | Small dropdown |
| `btn-single-send.png` | Send button (IM window) |
| `btn-middle-small.png` | Middle of conjoined buttons |
| `btn-right-small.png` | Right of conjoined buttons |
| `btn-left-small.png` | Left of conjoined buttons (optional) |
| `btn-no-border-right.png` | Borderless right button |

All button images are multi-state sprite sheets. States (left to right): normal, hover, pressed, disabled. Each state is the same width, stacked horizontally.

### Buddy List

> **Location:** `FriendList/` · **Format:** PNG

| File | Description | Reference |
|---|---|---|
| `me_bg.png` | "Me" panel background (profile area at top) | 169x61 (Teto) |
| `SearchBar.png` | Search bar (normal state) | ~70x32 |
| `SearchBar_h.png` | Search bar (hover) | — |
| `SearchBar_fc.png` | Search bar (focused) | — |
| `btn-single-status.png` | Status button in Me panel | — |
| `DisplayImgOverlay.png` | Avatar hover overlay | — |
| `AddBuddyPanel.png` | Bottom "Add Buddy" toolbar bg | ~64x30 |
| `BottomPluginEdge.png` | Visual separator above Add panel | — |
| `BottomPluginPanel.png` | Plugin toolbar background | ~37x5 |
| `PluginPanel.png` | Plugin area background | — |
| `PluginPanelFiller.png` | Plugin area filler | — |

### Selection & List Items

> **Location:** `images/` · **Format:** PNG (1px wide, tiled horizontally)

| File | Description |
|---|---|
| `bg_hover.png` | Buddy list item hover highlight |
| `bg_selected.png` | Buddy list item selection highlight |

### Scrollbar

> **Location:** `theme/` · **Format:** PNG

| File | Description |
|---|---|
| `scroll_vbg.png` | Vertical scrollbar track |
| `scroll_varrows.png` | Vertical scrollbar arrows |
| `scroll_v_grip.png` | Vertical scrollbar thumb grip |
| `scroll_thumb.png` | Vertical scrollbar thumb |
| `scroll_hbg.png` | Horizontal scrollbar track |
| `scroll_harrows.png` | Horizontal scrollbar arrows |
| `scroll_h_grip.png` | Horizontal scrollbar thumb grip |
| `scroll_thumb_h.png` | Horizontal scrollbar thumb |
| `scroll_buttons.png` | Scrollbar button sprites |

### Contact Card

> **Location:** `ContactCard/` · **Format:** PNG (no XML needed — images are auto-detected)

| File | Description | Reference |
|---|---|---|
| `background.png` | Card background | 254x136 (Teto) |
| `Frame.png` | Avatar frame | 38x304 (Teto) |
| `CallButton.png` | Call button icon | — |
| `card-CallButton.png` | Card call button | — |
| `SmsButton.png` | SMS button icon | — |
| `card-SmsButton.png` | Card SMS button | — |

### Miscellaneous

> **Location:** `theme/`

| File | Description |
|---|---|
| `headerbg.png` | List column header background |
| `grabber.png` | Draggable grabber icon |
| `grabbie.png` | Grip texture |
| `grabbie_back.png` | Grip background |
| `slotborder.png` | Plugin slot border |
| `slotborder_we_left.png` | Slot border left cap |
| `slotborder_we_right.png` | Slot border right cap |
| `ellipsis.png` | Text ellipsis (...) icon |
| `messenger.png` | Messenger tray icon |
| `checkbox.bmp` | Checkbox states (BMP format) |
| `radio.bmp` | Radio button states (BMP format) |
| `tabs.png` | Tab control sprite |
| `photoshare_slider_*.png` | Photo share slider elements |

> **Location:** `images/`

| File | Description |
|---|---|
| `resize_ctrl.png` | Window resize grip (bottom-right corner) |

---

## 9. The resizemargin System (Artwork Placement)

`resizemargin` is the **key** to placing character artwork on window borders, toolbars, and menu bars without stretching or tiling artifacts.

### Basic Concept

`resizemargin` divides an image into a 9-slice grid:

```
┌─────────┬───────────────────┬─────────┐
│ FIXED   │  STRETCH (tiles)  │  FIXED  │
│ top-left│   top center      │top-right│
├─────────┼───────────────────┼─────────┤
│ STRETCH │                   │ STRETCH │
│  left   │  CENTER (tiles)   │  right  │
├─────────┼───────────────────┼─────────┤
│ FIXED   │  STRETCH (tiles)  │  FIXED  │
│ bot-left│  bottom center    │bot-right│
└─────────┴───────────────────┴─────────┘
```

The **FIXED** corners never stretch. The **STRETCH** sections tile/repeat to fill available space. Place your artwork in the **FIXED** sections.

### Syntax

**Basic:**

```xml
resizemargin="left, top, right, bottom"
```

Defines the 4 border widths in pixels.

**Directional** (for finer control):

```xml
resizemargin_top="left_fixed, stretch, right_fixed, 0"
resizemargin_bottom="left_fixed, stretch, right_fixed, 0"
```

→ Controls horizontal edges (left-to-right slicing)

```xml
resizemargin_left="top_fixed, stretch, bottom_fixed, 0"
resizemargin_right="top_fixed, stretch, bottom_fixed, 0"
```

→ Controls vertical edges (top-to-bottom slicing)

**Rule:** `left_fixed + stretch + right_fixed = image dimension`
- For horizontal: sum = image width
- For vertical: sum = image height

### Practical Examples

#### Example 1: Character art on the RIGHT side of the title bar

`mainframewindow.png` is 190px wide. You want 170px on the right for artwork.

```xml
resizemargin_top="10,0,170,0"
```

| Value | Meaning |
|---|---|
| `10` | 10px left (fixed, won't stretch) |
| `0` | 0px stretch (nothing tiles — but keep ≥1 for safety) |
| `170` | **170px right (YOUR ARTWORK — fixed, won't stretch)** |
| `0` | (unused) |

> Total: 10 + 0 + 170 = 180 ≠ 190 — the remaining 10px is part of the main resizemargin border area.

> **Note:** The "stretch" value of 0 means no tiling. Consider using 1 with a single solid-color pixel column for cleaner behavior.

#### Example 2: Character art on the IM toolbar

`toolbar_back.png` is 205px wide. You want 144px on the right for Teto artwork.

```xml
resizemargin="0,0,144,0"    <!-- on IMWnd.ToolbarArea override -->
```

- 0px left, 0px top
- **144px right** (artwork area, won't stretch)
- 0px bottom
- The remaining 61px on the left will tile to fill the toolbar width.

#### Example 3: Menu bar artwork

`menubar.png` is 143px wide. You want 91px on the right for artwork.

```xml
resizemargin_baritem="4,0,91,0"
```

- 4px left fixed
- 0px stretch (or use a 1px solid column)
- **91px right fixed** (artwork)
- Total: 4 + 0 + 91 = 95px (+48px for state height)

### Tips for Custom Artwork

- Keep the stretch area as small as possible (0-2px)
- Use a solid color or very subtle gradient in the stretch row/column
- Place all detailed artwork in the FIXED sections
- Start with conservative values and adjust
- The title bar artwork area is typically 140-190px on the right
- Toolbar artwork is typically 100-200px on the right
- Menu bar artwork is typically 50-100px on the right
- Test at various window sizes to ensure no artifacts

### Choosing Your Artwork Dimensions

There's no single "correct" size. It depends on your artwork:

**Small artwork** (icon/badge in corner):

```xml
Frame:   resizemargin_top="10,1,50,0"     → 50px
Toolbar: resizemargin="0,0,60,0"          → 60px
Menu:    resizemargin_baritem="4,1,40,0"   → 40px
```

**Medium artwork** (half-body character):

```xml
Frame:   resizemargin_top="10,0,130,0"    → 130px
Toolbar: resizemargin="0,0,144,0"         → 144px
Menu:    resizemargin_baritem="4,0,91,0"   → 91px
```

**Large artwork** (wide character art):

```xml
Frame:   resizemargin_top="10,0,170,0"    → 170px
Toolbar: resizemargin="0,0,190,0"         → 190px
Menu:    resizemargin_baritem="4,0,120,0"  → 120px
```

> **Remember:** larger artwork means the window needs to be wider for everything to display correctly. Test your minimum window width!

---

## 10. Dark vs Light Theme Guide

Your skin can be either **dark** (like Ruby Red - Teto) or **light** (like Icy Blue - Hatsune Miku). Here's what changes between them.

### Dark Theme Principles

- Background colors: deep/saturated (e.g., `133,3,35` ruby red)
- Text colors: light/white (e.g., `f3f3f3`)
- Selection: slightly lighter than background (e.g., `9d4a5e`)
- Menu item text: dark on dropdown (`333333`), light on bar (`dcdcdc`)
- Hyperlinks: must be bright enough to read on dark bg
- All syscolors backgrounds → match your dark background
- All syscolors text → match your light text

### Light Theme Principles

- Background colors: light/pastel (e.g., `219,231,245` icy blue)
- Text colors: dark/black (e.g., `333333`)
- Selection: medium-dark accent (e.g., `496992` steel blue)
- Menu item text: dark on dropdown (`333333`), light on bar (`dcdcdc`)
- Hyperlinks: standard blue tones work well
- All syscolors backgrounds → match your light background
- All syscolors text → match your dark text

### Switching Checklist

When converting between dark and light:

- [ ] Flip `mainwndbackcolor` (dark ↔ light)
- [ ] Flip `text` (light text on dark bg ↔ dark text on light bg)
- [ ] Flip `buttontext` / `buttontext_h` / `buttontext_p`
- [ ] Flip `selected_bg` (ensure contrast with `selected_text`)
- [ ] Flip `text_hyperlink` (readable on new background?)
- [ ] Flip `text_sys_notif`
- [ ] Flip `tooltip` / `tooltip_text` if needed
- [ ] Update ALL syscolors `COLOR_WINDOW`, `COLOR_BTNFACE`, `COLOR_3DFACE` → new bg
- [ ] Update ALL syscolors `COLOR_WINDOWTEXT`, `COLOR_BTNTEXT` → new text
- [ ] Update `COLOR_HIGHLIGHT` → new selection color
- [ ] Verify `toolbar_border` matches new background
- [ ] Verify `slotborder_gap` has good contrast
- [ ] Check `groupheader` and `groupheader_text` contrast
- [ ] Test that `ctrl_focused_bg` is visible on new background
- [ ] Rebuild/recolor all button and scrollbar images for new scheme

### Color Harmony Tips

- Pick **one** primary hue for your skin
- Use it at full saturation for: `mainwndbackcolor`, `groupheader`, `selected_bg`, `ctrl_focused_bg`
- Use it desaturated/lighter for: `toolbar_border`, `slotborder_gap`
- Use complementary or neutral colors for text
- Keep menubar/toolbar text consistent (`dcdcdc` normal, `ffffff` hover)
- Test with multiple buddy list entries and IM conversations

---

## 11. Step-by-Step: Creating a New Skin

### Preparation

- [ ] Choose your color scheme (primary hue, dark or light)
- [ ] Gather your artwork (character images, logos, etc.)
- [ ] Decide artwork size: small / medium / large (see [Section 9](#9-the-resizemargin-system-artwork-placement))
- [ ] Pick a base skin to copy `.rgn` files from:
  - Use Icy Blue if making a light skin
  - Use Ruby Red if making a dark skin
  - Or copy from any existing skin — `.rgn` files are interchangeable

### Step 1: Create Folder Structure

- [ ] Copy "Skin Template" folder to a new folder named after your skin
- [ ] Copy `.rgn` files from your chosen base skin into `theme/`:
  - `framewindow.rgn`
  - `mainframewindow.rgn`
  - `toolwindow.rgn`

### Step 2: Set Identity (`Localized.xml`)

- [ ] Edit `Localized.xml`
- [ ] Set `LocalizedName` to your skin's display name

### Step 3: Define Colors (`colors.xml`)

- [ ] Open `colors.xml`
- [ ] Set `mainwndbackcolor` (your primary background)
- [ ] Set all text colors for contrast
- [ ] Set selection/highlight colors
- [ ] Set button text colors
- [ ] Set menu text colors
- [ ] Set toolbar & slot border colors
- [ ] Configure `syscolors` block:
  - All background syscolors → your `mainwndbackcolor` (decimal R,G,B)
  - All text syscolors → your text color (decimal R,G,B)
  - Highlight → your selection color
- [ ] Test in Yahoo! Messenger — colors alone make a big difference!

### Step 4: Create Images

- [ ] Create/recolor window frame images:
  - `theme/mainframewindow.png` (buddy list frame)
  - `theme/framewindow.png` (IM window frame)
  - `theme/toolwindow.png` (popup frame)
- [ ] Create toolbar/menu images with your color + artwork:
  - `theme/menubar.png` (menu bar background)
  - `IMWindow/toolbar_back.png` (IM toolbar background)
- [ ] Create buddy list images:
  - `FriendList/me_bg.png` (profile panel background)
  - `FriendList/SearchBar.png`, `SearchBar_h.png`, `SearchBar_fc.png`
- [ ] Recolor accent images:
  - `images/bg_hover.png` (list hover)
  - `images/bg_selected.png` (list selection)
  - `theme/pushbuttons.png` (button states)
- [ ] Optional: ContactCard images, scrollbar images, etc.

### Step 5: Configure Theme (`theme/theme.xml`)

- [ ] Open `theme/theme.xml`
- [ ] Set `mainframewindow` resizemargin to match your image:
  - `resizemargin_top` right value = your artwork width
- [ ] Set `framewindow` resizemargin:
  - If adding artwork to IM frames, adjust `resizemargin_top`
  - If filling edge gaps, reduce right border (5→2)
- [ ] Set menu `resizemargin_baritem` to match `menubar.png` artwork area
- [ ] Test window frame rendering at different sizes

### Step 6: Configure Layouts (`FriendList.xml`, `IMWindow.xml`)

- [ ] Edit `FriendList/FriendList.xml`:
  - Set MePanel resizemargin right value for artwork
- [ ] Edit `IMWindow/IMWindow.xml`:
  - Set `IMWnd.ToolbarArea` imagefile and resizemargin
  - Optionally fix toolbar gap: `IMWnd.MainToolbarArea` margin=`"7,0,0,0"`
  - Optionally adjust status bar alpha values

### Step 7: Test & Polish

- [ ] Load skin in Yahoo! Messenger
- [ ] Check buddy list at various sizes
- [ ] Check IM window at various sizes (especially maximized)
- [ ] Check menu dropdowns
- [ ] Check dialogs (Add Contact, Preferences, etc.)
- [ ] Verify text readability everywhere
- [ ] Check for stretching/tiling artifacts
- [ ] Add `preview.png` for the skin picker

---

## 12. Troubleshooting

| Problem | Solution |
|---|---|
| **Images are tiling/repeating visibly** | Increase the fixed area values in `resizemargin`. Decrease the stretch middle value (use 0 or 1). Ensure stretch area is a solid color. |
| **Gap between menu bar and toolbar** | Override `IMWnd.MainToolbarArea` margin to `"7,0,0,0"` (removes the default 2px top gap). |
| **Gap on the right edge of toolbar/menubar** | Reduce `framewindow` resizemargin right border (5→2). Extend your toolbar/menubar images into that space. See `THEME_CUSTOMIZATION_GUIDE.md` in Ruby Red - Teto for details. |
| **Colors look wrong in dialogs/system controls** | Check your `syscolors` block in `colors.xml`. All background colors should match `mainwndbackcolor`. Text colors should match your text color scheme. |
| **Application crashes when loading skin** | Don't set resizemargin values > image dimensions. Ensure all values are non-negative integers. Leave at least a few pixels margin. Test with conservative values first. |
| **Text is invisible (same color as background)** | Check dark/light switching checklist ([Section 10](#10-dark-vs-light-theme-guide)). Ensure text and background have sufficient contrast. Check both active and inactive title bar colors. |
| **Menu text is invisible** | Menu dropdowns use `menuitemtext` (usually dark: `333333`). Menu bars use `mainmenubartext`/`menubartext`. These are deliberately different — dropdowns are always light-background so text should be dark. |
| **Preview doesn't show in skin picker** | Add `preview.png` in your skin's root folder (~150x200px). |
| **Some images don't load** | Check file paths — they're relative to the subfolder. `theme.xml` images → `theme/` folder. `FriendList.xml` images → `FriendList/` folder. `IMWindow.xml` images → `IMWindow/` folder. General images → `images/` folder. |

---

## 13. Files Reference Table

### Root `/`

| File | Required | Purpose |
|---|---|---|
| `Localized.xml` | **YES** | Skin display name |
| `colors.xml` | **YES** | Color palette & syscolors |
| `preview.png` | Recommended | Skin picker thumbnail |

### `theme/`

| File | Required | Purpose |
|---|---|---|
| `theme.xml` | Optional | Window frames & controls |
| `framewindow.png` | Optional | IM window frame image |
| `mainframewindow.png` | Optional | Main window frame image |
| `toolwindow.png` | Optional | Popup window frame image |
| `framewindow.rgn` | Optional | Window region shape |
| `mainframewindow.rgn` | Optional | Window region shape |
| `toolwindow.rgn` | Optional | Window region shape |
| `Yahoo!_Messenger.png` | Optional | Title bar logo |
| `menubar.png` | Optional | Menu bar background |
| `menuitem.png` | Optional | Menu dropdown item bg |
| `pushbuttons.png` | Optional | Standard button sprite |
| `headerbg.png` | Optional | List header background |
| `slotborder*.png` | Optional | Plugin slot borders |
| `scroll_*.png` | Optional | Scrollbar images |

### `FriendList/`

| File | Required | Purpose |
|---|---|---|
| `FriendList.xml` | Optional | Buddy list overrides |
| `me_bg.png` | Optional | Profile panel background |
| `SearchBar*.png` | Optional | Search bar states |
| `btn-single-status.png` | Optional | Status button |
| `DisplayImgOverlay.png` | Optional | Avatar overlay |

### `IMWindow/`

| File | Required | Purpose |
|---|---|---|
| `IMWindow.xml` | Optional | IM window overrides |
| `toolbar_back.png` | Optional | IM toolbar background |

### `images/`

| File | Required | Purpose |
|---|---|---|
| `bg_hover.png` | Optional | List item hover bg |
| `bg_selected.png` | Optional | List item selected bg |
| `btn-single-*.png` | Optional | Various button images |
| `resize_ctrl.png` | Optional | Window resize grip |

### `ContactCard/`

| File | Required | Purpose |
|---|---|---|
| `*.png` | Optional | Contact card images |

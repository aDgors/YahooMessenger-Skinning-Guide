# Yahoo Messenger Skin Customization Guide

## Overview
This guide documents how to customize Yahoo Messenger skins to prevent image stretching/tiling and eliminate gaps when adding custom artwork to window borders and menu areas.

## Understanding resizemargin

The `resizemargin` parameter controls how images are divided for stretching and tiling in the Yahoo Messenger skinning system.

### Basic Syntax
```xml
resizemargin="left, top, right, bottom"
```
- Defines the border widths (in pixels) for each edge of the window frame

### Directional Resizemargins

**For horizontal stretching (top/bottom edges):**
```xml
resizemargin_top="left_fixed, stretch, right_fixed, 0"
resizemargin_bottom="left_fixed, stretch, right_fixed, 0"
```

**For vertical stretching (left/right edges):**
```xml
resizemargin_left="top_fixed, stretch, bottom_fixed, 0"
resizemargin_right="top_fixed, stretch, bottom_fixed, 0"
```

**Parameters:**
- `left_fixed` / `top_fixed`: Pixels from the left/top that remain fixed (won't stretch)
- `stretch`: Pixels in the middle that will stretch/tile to fill extra space
- `right_fixed` / `bottom_fixed`: Pixels from the right/bottom that remain fixed
- Last value: Usually 0

**Important:** The sum should equal your image dimension:
- For horizontal: `left_fixed + stretch + right_fixed = image width`
- For vertical: `top_fixed + stretch + bottom_fixed = image height`

### Tips for Minimal Tiling
- Keep the stretch value small (1-2px) to minimize visible tiling
- Place important graphics in the fixed sections
- Use a solid color or subtle pattern in the stretch area

## Problem 1: Menu Bar Stretching

### Issue
The menubar.png image was stretching and creating visible artifacts when the window was maximized.

### Solution
Add resizemargin to the menu style to control stretching:

**File: `theme/theme.xml`**
```xml
<style id="menu"		
    imagefile="menu_bg.png"
    imagefile_buttons="menu_scroll.bmp"
    imagefile_menuitem="menuitem.png"
    imagefile_mainbaritem="mainmenubar.png"
    imagefile_baritem="menubar.png"
    resizemargin_baritem="4,0,91,0" />
```

**Breakdown:**
- `4px` fixed on left
- `0px` stretch area (no stretching)
- `91px` fixed on right (artwork area)
- Total image width: 95px

## Problem 2: Gap Between Menubar and Toolbar

### Issue
There was a 2px gap between the menubar and toolbar_back due to margin settings.

### Solution
Override the margin on IMWnd.MainToolbarArea:

**File: `IMWindow/IMWindow.xml`**
```xml
<override id="IMWnd.MainToolbarArea" margin="7,0,0,0" />
<override id="IMWnd.ToolbarArea" imagefile="toolbar_back.png" resizemargin="0,0,144,0"/>
```

**Changes:**
- Changed margin from `"7,2,0,0"` to `"7,0,0,0"` (removed 2px top margin)
- Set toolbar_back resizemargin to reserve 144px on the right for artwork

## Problem 3: 3px Right Edge Gap

### The Real Issue
The framewindow.png has a built-in border that creates a content area. The menubar and toolbar_back were rendered inside this content area, leaving a 3px gap from the window edge.

### Solution
1. **Modify framewindow.png**: Add 3px of artwork into the right border area
2. **Reduce the right border width**: Change from 5px to 2px to allow content to extend into the artwork area

**File: `theme/theme.xml`**
```xml
<style id="framewindow" imagefile="framewindow.png"
    resizemargin="3,28,2,4"
    resizemargin_top="10,0,170,0"
    resizemargin_bottom="0,0,9,0"
    resizemargin_left="0,1,0,9"
    resizemargin_right="0,1,0,9"
    active_font="defaultfontbold" active_textcolor="activemaincaption"
    inactive_font="defaultfontbold" inactive_textcolor="itactivemaincaption"
    icon_xpos="10" icon_ypos="6"
    title_xpos="30" title_ypos="0"/>
```

**Key change:** `resizemargin="3,28,2,4"` - reduced right border from 5 to 2

## Complete Implementation Steps

### Step 1: Prepare Your Images

1. **menubar.png**: 95px wide
   - 4px left margin (solid color/background)
   - 91px right side (your character artwork)

2. **toolbar_back.png**: 144px wide
   - All space can be used for artwork

3. **framewindow.png**: 
   - Add 3px of artwork into the right border area
   - Adjust total width accordingly

### Step 2: Update theme.xml

```xml
<!-- Frame window with reduced right border -->
<style id="framewindow" imagefile="framewindow.png"
    resizemargin="3,28,2,4"
    resizemargin_top="10,0,170,0"
    resizemargin_bottom="0,0,9,0"
    resizemargin_left="0,1,0,9"
    resizemargin_right="0,1,0,9"
    active_font="defaultfontbold" active_textcolor="activemaincaption"
    inactive_font="defaultfontbold" inactive_textcolor="itactivemaincaption"
    icon_xpos="10" icon_ypos="6"
    title_xpos="30" title_ypos="0"/>

<!-- Menu bar with no-stretch resizemargin -->
<style id="menu"		
    imagefile="menu_bg.png"
    imagefile_buttons="menu_scroll.bmp"
    imagefile_menuitem="menuitem.png"
    imagefile_mainbaritem="mainmenubar.png"
    imagefile_baritem="menubar.png"
    resizemargin_baritem="4,0,91,0" />
```

### Step 3: Update IMWindow/IMWindow.xml

```xml
<section id="IMWindow">
  <!-- Toolbar area -->
  <override id="IMWnd.MainToolbarArea" margin="7,0,0,0" />
  <override id="IMWnd.ToolbarArea" imagefile="toolbar_back.png" resizemargin="0,0,144,0"/>
  
  <!-- TextAd area -->
  <override id="IMWnd.StatusBarTag" alpha="69" />
  <override id="IMWnd.StatusBar" alpha="107" />
  <override id="IMWnd.StatusBarUrl" alpha="107" />
  <override id="IMWnd.StatusBarUrl2" alpha="107" />
</section>
```

## Troubleshooting

### Images are tiling/repeating
- Increase the fixed area values in resizemargin
- Decrease the stretch value (middle parameter)

### Gap still visible on right edge
- Check framewindow.png right border width
- Ensure resizemargin right value matches
- Verify menubar/toolbar images extend into that space

### Layout breaks when window resizes
- Verify resizemargin values sum to actual image dimensions
- Check that stretch areas use subtle/solid colors

### Application crashes
- Don't set resizemargin values too close to image dimensions (leave at least a few pixels)
- Ensure all values are positive integers
- Test with conservative values first (like 10px minimums for fixed areas)

## Files Modified

1. `theme/theme.xml` - Window frame and menu styling
2. `IMWindow/IMWindow.xml` - IM window toolbar customization
3. `theme/framewindow.png` - Window border image
4. `theme/menubar.png` - Menu bar background image
5. `images/toolbar_back.png` - Toolbar background image (in IMWindow folder)

## Key Learnings

1. **The framewindow border is part of the image itself** - Content is rendered inside it
2. **Margins in XML control spacing** - Override them to eliminate gaps
3. **resizemargin controls stretching** - Plan image dimensions carefully
4. **Small stretch values minimize artifacts** - Use 0-2px for best results
5. **Fixed sections preserve artwork** - Always put graphics in fixed areas

---

**Created:** February 2026  
**Skin:** Ruby Red - Teto  
**Application:** Yahoo Messenger

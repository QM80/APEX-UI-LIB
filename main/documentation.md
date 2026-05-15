# APEX UI Library

A feature-rich, themeable Roblox UI library for creating in-game menus with pages, sections, and a wide range of interactive components.

---

## Table of Contents

- [Setup](#setup)
- [Window](#window)
- [Pages & SubPages](#pages--subpages)
- [Sections](#sections)
- [Components](#components)
  - [Toggle](#toggle)
  - [Button](#button)
  - [Slider](#slider)
  - [Dropdown](#dropdown)
  - [Searchbox](#searchbox)
  - [Textbox](#textbox)
  - [Label](#label)
  - [Colorpicker](#colorpicker)
  - [Keybind](#keybind)
- [Extras](#extras)
  - [Watermark](#watermark)
  - [Keybind List](#keybind-list)
  - [Notifications](#notifications)
  - [Settings Page](#settings-page)
- [Theming](#theming)
- [Flags & Config System](#flags--config-system)
- [Unloading](#unloading)

---

## Setup

Call `Library:SetFolder` before creating any UI. This sets up the directory structure and downloads required assets (images, font).

```lua
Library:SetFolder("MyCheat")
```

This creates the following folders:
- `MyCheat/` — root directory
- `MyCheat/Configs` — saved configs
- `MyCheat/Assets` — images and font files

---

## Window

The entry point for all UI. Creates the main draggable, resizable window.

```lua
local Window = Library:Window({
    Size = UDim2.new(0, 609, 0, 507),  -- optional
    FadeTime = 0.4,                     -- optional
    UITitle = "My Script",              -- optional, shown in title bar
})
```

### `Window:SetOpen(bool)`
Manually show or hide the window. The menu keybind (default `RightControl`) toggles it automatically.

```lua
Window:SetOpen(true)
Window:SetOpen(false)
```

---

## Pages & SubPages

Pages appear as tabs in the left sidebar.

```lua
local MyPage = Window:Page({
    Name = "Combat",
    Columns = 2,       -- number of section columns (default: 2)
    SubPages = false,  -- set true to use SubPages instead of columns
})
```

### SubPages

When `SubPages = true`, the page contains a tab bar of sub-pages instead of direct columns.

```lua
local MyPage = Window:Page({ Name = "Visuals", SubPages = true })

local SubPage = MyPage:SubPage({
    Name = "ESP",
    Columns = 2,
})
```

### Player List Page

A special pre-built page that lists all players in the server with avatar, username, account age, and a status dropdown.

```lua
local Playerlist = MyPage:Playerlist({
    Callback = function(Player, Status, Team)
        -- fires when a player is selected
    end
})
```

---

## Sections

Sections are containers inside a page column that group related elements.

```lua
local Section = MyPage:Section({
    Name = "Aimbot",
    Side = 1,   -- which column (1 or 2)
})
```

---

## Components

All components are created on a `Section` object.

### Toggle

A simple on/off toggle.

```lua
local Toggle = Section:Toggle({
    Name = "Enable Aimbot",
    Flag = "AimbotEnabled",
    Default = false,
    Tooltip = {               -- optional hover tooltip
        Name = "Aimbot",
        Description = "Enables the aimbot"
    },
    Callback = function(Value)
        print("Aimbot:", Value)
    end
})
```

**Methods:**
```lua
Toggle:Set(true)          -- set value programmatically
Toggle:Get()              -- returns current boolean value
Toggle:SetText("New Name") -- update label text
Toggle:SetVisibility(bool) -- show/hide the element
```

Toggles can have an inline **Colorpicker** or **Keybind** attached:

```lua
Toggle:Colorpicker({
    Flag = "AimbotColor",
    Default = Color3.fromRGB(255, 0, 0),
    Alpha = 0,
    Callback = function(Color, Alpha) end
})

Toggle:Keybind({
    Flag = "AimbotKey",
    Default = Enum.KeyCode.E,
    Mode = "Toggle", -- "Toggle", "Hold", or "Always"
    Callback = function(Active) end
})
```

---

### Button

A row of one or more clickable buttons.

```lua
local Button = Section:Button()

Button:Add("Teleport", function()
    print("Teleport pressed")
end)

Button:Add("Reset", function()
    print("Reset pressed")
end)
```

**Methods:**
```lua
Button:SetVisibility(bool)
```

---

### Slider

A draggable value slider.

```lua
local Slider = Section:Slider({
    Name = "FOV",
    Flag = "AimbotFOV",
    Min = 0,
    Max = 360,
    Default = 90,
    Decimals = 1,       -- rounding precision
    Suffix = "°",       -- appended to value display
    Callback = function(Value)
        print("FOV:", Value)
    end
})
```

**Methods:**
```lua
Slider:Set(120)        -- set value programmatically
Slider:Get()           -- returns current number value
Slider:SetVisibility(bool)
```

---

### Dropdown

A single or multi-select dropdown menu.

```lua
local Dropdown = Section:Dropdown({
    Name = "Aim Part",
    Flag = "AimbotPart",
    Items = { "Head", "Torso", "HumanoidRootPart" },
    Default = "Head",
    Multi = false,
    Callback = function(Value)
        print("Selected:", Value)
    end
})
```

**Methods:**
```lua
Dropdown:Set("Torso")           -- set selected option
Dropdown:Get()                   -- returns current value (string or table if Multi)
Dropdown:Add("NewOption")        -- add an option at runtime
Dropdown:Remove("OldOption")     -- remove an option
Dropdown:Refresh({ "A", "B" })  -- replace all options
Dropdown:SetVisibility(bool)
```

---

### Searchbox

A listbox with a live search/filter input. Good for large lists.

```lua
local Searchbox = Section:Searchbox({
    Name = "PlayerSelect",
    Flag = "SelectedPlayer",
    Items = { "Player1", "Player2", "Player3" },
    Multi = false,
    Default = nil,
    Callback = function(Value)
        print("Selected:", Value)
    end
})
```

Supports the same `Add`, `Remove`, `Refresh`, `Set`, and `Get` methods as Dropdown.

---

### Textbox

A text input field.

```lua
local Textbox = Section:Textbox({
    Name = "Custom Tag",
    Flag = "PlayerTag",
    Default = "",
    Placeholder = "Enter tag...",
    Numeric = false,    -- if true, only allows numbers
    Finished = false,   -- if true, callback fires only on Enter
    Callback = function(Value)
        print("Tag:", Value)
    end
})
```

**Methods:**
```lua
Textbox:Set("hello")
Textbox:Get()
Textbox:SetVisibility(bool)
```

---

### Label

A static text label. Can optionally have an inline Colorpicker or Keybind.

```lua
local Label = Section:Label("Player Info")

-- With a tooltip:
local Label = Section:Label("Player Info", {
    Name = "Info",
    Description = "Displays player information"
})
```

**Methods:**
```lua
Label:SetText("New Text")
Label:SetVisibility(bool)

-- Inline Colorpicker
Label:Colorpicker({
    Flag = "ESPColor",
    Default = Color3.fromRGB(255, 255, 255),
    Alpha = 0,
    Callback = function(Color, Alpha) end
})

-- Inline Keybind
Label:Keybind({
    Flag = "ESPKey",
    Default = Enum.KeyCode.H,
    Mode = "Toggle",
    Callback = function(Active) end
})
```

---

### Colorpicker

A full color picker with HSV palette, hue bar, alpha slider, and optional animation/other tabs.

Colorpickers are typically created inline on a `Toggle` or `Label` (see above). When created this way, `Pages = true` enables the Color / Animations / Other tab system.

**Methods:**
```lua
Colorpicker:Set(Color3.fromRGB(255, 0, 0), 0)  -- set color and alpha
Colorpicker:Get()  -- returns Color3 value
```

---

### Keybind

A key binding element. Left-click to pick a key, right-click for mode selection.

Keybinds are typically created inline on a `Toggle` or `Label` (see above).

**Modes:**
- `Toggle` — fires callback each press, alternating true/false
- `Hold` — true while key held, false on release
- `Always` — always true while active

**Methods:**
```lua
Keybind:Set({ Key = "Enum.KeyCode.E", Mode = "Hold" })
Keybind:Get()          -- returns key, mode, toggled state
Keybind:SetMode("Hold")
Keybind:SetOpen(bool)  -- open/close the mode picker popup
```

---

## Extras

### Watermark

A small draggable HUD element displayed at the bottom of the screen.

```lua
local Watermark = Library:Watermark("MyScript | v1.0")

Watermark:SetText("MyScript | v1.0 | 144 FPS")
Watermark:SetVisibility(true)
```

---

### Keybind List

Displays a list of active keybinds on-screen. Automatically populated when Keybind elements are created.

```lua
local KeybindList = Library:KeybindList()

KeybindList:SetVisibility(true)
```

---

### Notifications

Sends a temporary toast notification to the bottom-right of the screen.

```lua
Library:Notification("Title", "Description text here", 5) -- 5 = duration in seconds
```

---

### Settings Page

Creates a pre-built Settings page with theming, config management, and general settings tabs.

```lua
local SettingsPage = Library:CreateSettingsPage(Window, Watermark, KeybindList)
```

This automatically adds:
- **Theming** — live color pickers for every theme color
- **Configs** — save, load, create, and delete config files
- **Settings** — toggle watermark/keybind list, adjust tween/fade speed, change menu keybind, unload

---

## Theming

The default theme colors and their keys:

| Key | Default |
|---|---|
| `Background` | `#101214` |
| `Border` | `#252824` |
| `Inline` | `#101214` |
| `Page Background` | `#101214` |
| `Outline` | `#252824` |
| `Element` | `#17191B` |
| `Gradient` | `RGB(208, 208, 208)` |
| `Text` | `#EDEFF1` |
| `Text Stroke` | `#000000` |
| `Placeholder Text` | `#b9b9b9` |
| `Accent` | `#854EAC` |

Change a theme color at runtime:

```lua
Library:ChangeTheme("Accent", Color3.fromRGB(255, 100, 0))
```

---

## Flags & Config System

Every interactive element has a `Flag` string that maps to a value in `Library.Flags`.

```lua
print(Library.Flags["AimbotEnabled"])  -- true/false
print(Library.Flags["AimbotFOV"])      -- number
print(Library.Flags["AimbotPart"])     -- string or table
```

### Saving a Config

```lua
local json = Library:GetConfig()
writefile("config.json", json)
```

### Loading a Config

```lua
local json = readfile("config.json")
Library:LoadConfig(json)
```

### Deleting a Config

```lua
Library:DeleteConfig("config.json")
```

---

## Unloading

Cleanly disconnects all connections, closes threads, and destroys all UI.

```lua
Library:Unload()
```

After calling this, `Library` and `getgenv().Library` are set to `nil`.

---

## Menu Keybind

Default toggle keybind is `RightControl`. Change it at any time:

```lua
Library.MenuKeybind = tostring(Enum.KeyCode.Insert)
```

---

## Full Example

```lua
Library:SetFolder("MyScript")

local Window = Library:Window({
    UITitle = "MyScript",
    FadeTime = 0.3,
})

local Watermark  = Library:Watermark("MyScript | v1.0")
local KeybindList = Library:KeybindList()

local CombatPage = Window:Page({ Name = "Combat", Columns = 2 })

local AimbotSection = CombatPage:Section({ Name = "Aimbot", Side = 1 })

local AimbotToggle = AimbotSection:Toggle({
    Name = "Enable Aimbot",
    Flag = "AimbotEnabled",
    Default = false,
    Callback = function(Value)
        -- your code here
    end
})

AimbotToggle:Colorpicker({
    Flag = "AimbotColor",
    Default = Color3.fromRGB(255, 0, 0),
    Alpha = 0,
})

AimbotToggle:Keybind({
    Flag = "AimbotKey",
    Default = Enum.KeyCode.E,
    Mode = "Toggle",
})

AimbotSection:Slider({
    Name = "FOV",
    Flag = "AimbotFOV",
    Min = 0,
    Max = 360,
    Default = 90,
    Suffix = "°",
    Callback = function(Value) end
})

AimbotSection:Dropdown({
    Name = "Aim Part",
    Flag = "AimbotPart",
    Items = { "Head", "Torso" },
    Default = "Head",
    Callback = function(Value) end
})

Library:CreateSettingsPage(Window, Watermark, KeybindList)
```

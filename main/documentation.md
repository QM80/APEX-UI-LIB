# APEX UI Library — Documentation

> A Roblox UI library for building feature-rich exploit menus with pages, sections, and components.

---

## Table of Contents

- [Installation](#installation)
- [Window](#window)
- [Watermark](#watermark)
- [KeybindList](#keybindlist)
- [Pages](#pages)
  - [SubPages](#subpages)
  - [Settings Page](#settings-page)
- [Sections](#sections)
- [Components](#components)
  - [Toggle](#toggle)
  - [Slider](#slider)
  - [Dropdown](#dropdown)
  - [Button](#button)
  - [Colorpicker](#colorpicker)
  - [Keybind](#keybind)
  - [Searchbox](#searchbox)
  - [Playerlist](#playerlist)
- [Notifications](#notifications)

---

## Installation

Load the library at the top of your script using `loadstring` and `HttpGet`.

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/QM80/APEX-UI-LIB/refs/heads/main/main/src/uilibrary.luau"))()
```

---

## Window

The `Window` is the root container for your entire UI. Create it right after loading the library.

```lua
local Window = Library:Window({
    FadeTime = 0.3,
    UITitle = "My Script",
})
```

| Property | Type | Description |
|---|---|---|
| `FadeTime` | `number` | How long (in seconds) the UI takes to fade in/out |
| `UITitle` | `string` | The title displayed at the top of the window |

---

## Watermark

A small overlay that displays live info like FPS, ping, and player name.

```lua
local Watermark = Library:Watermark("0 FPS | 0 ms")
```

### Methods

```lua
Watermark:SetText("60 FPS | 12 ms | PlayerName ( 123456 )")
```

### Live FPS & Ping Example

```lua
local fps = 0
local ping = 0
local frameCount = 0
local lastUpdate = tick()
local plr = game:GetService("Players").LocalPlayer

game:GetService("RunService").RenderStepped:Connect(function()
    frameCount += 1

    local now = tick()
    if now - lastUpdate >= 0.5 then
        fps = frameCount
        frameCount = 0
        lastUpdate = now

        ping = math.floor(game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue())

        Watermark:SetText(fps .. " FPS | " .. ping .. " ms | " .. plr.Name .. " ( " .. plr.UserId .. " )")
    end
end)
```

---

## KeybindList

Creates a list overlay that shows all active keybinds in the UI.

```lua
local KeybindList = Library:KeybindList()
```

> The `KeybindList` is passed into `CreateSettingsPage` so keybinds registered on components appear here automatically.

---

## Pages

Pages are the top-level tabs in the window. Each page can have 1 or 2 columns.

```lua
local CombatPage  = Window:Page({ Name = "Combat",  SubPages = true })
local PlayerPage  = Window:Page({ Name = "Player",  Columns = 2 })
local VisualsPage = Window:Page({ Name = "Visuals", Columns = 2 })
local PlayersPage = Window:Page({ Name = "Players", Columns = 2 })
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | The tab label shown in the window |
| `Columns` | `number` | `1` or `2` columns layout for sections |
| `SubPages` | `bool` | If `true`, this page uses sub-tabs instead of columns |

---

### SubPages

When a page has `SubPages = true`, you add sub-tabs inside it instead of sections directly.

```lua
local WeaponSubPage = CombatPage:SubPage({ Name = "Weapon", Columns = 2 })
local AimbotSubPage = CombatPage:SubPage({ Name = "Aimbot", Columns = 2 })
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | The sub-tab label |
| `Columns` | `number` | `1` or `2` column layout for sections inside this sub-page |

---

### Settings Page

Creates a built-in settings page that handles theme, config saving, and keybind list automatically.

```lua
local SettingsPage = Library:CreateSettingsPage(Window, Watermark, KeybindList)
```

| Argument | Description |
|---|---|
| `Window` | Your main window instance |
| `Watermark` | Your watermark instance |
| `KeybindList` | Your keybind list instance |

---

## Sections

Sections group related components inside a page or sub-page. Use `Side` to place them in column 1 or 2.

```lua
local MySection = MyPage:Section({ Name = "My Section", Side = 1 })
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | The section header label |
| `Side` | `number` | `1` = left column, `2` = right column |

---

## Components

All components are created by calling methods on a **Section**.

---

### Toggle

A simple on/off switch.

```lua
local Toggle = MySection:Toggle({
    Name = "Enabled",
    Flag = "MyToggleFlag",
    Default = false,
    Callback = function(Value)
        print(Value) -- true or false
    end
})
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | Label shown next to the toggle |
| `Flag` | `string` | Unique ID used to reference this value globally |
| `Default` | `bool` | Starting value |
| `Callback` | `function` | Called with `true`/`false` when toggled |
| `ToolTip` | `table` | *(Optional)* Hover tooltip with `Name` and `Description` |

#### ToolTip Example

```lua
MySection:Toggle({
    Name = "Silent Aim",
    ToolTip = {
        Name = "Silent aim",
        Description = "Redirects bullets to the closest target from the mouse"
    },
    Flag = "SilentAimEnabled",
    Default = false,
    Callback = function(Value) end
})
```

#### Methods

```lua
Toggle:Set(true)            -- Set value programmatically
Toggle:Get()                -- Returns current boolean value
Toggle:SetVisibility(false) -- Show or hide the toggle
```

---

### Slider

A draggable number slider.

```lua
local Slider = MySection:Slider({
    Name = "Speed",
    Flag = "SpeedFlag",
    Min = 0,
    Max = 100,
    Default = 16,
    Decimals = 1,
    Suffix = "studs/s",
    Callback = function(Value)
        print(Value) -- number
    end
})
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | Label shown above the slider |
| `Flag` | `string` | Unique ID for this value |
| `Min` | `number` | Minimum value |
| `Max` | `number` | Maximum value |
| `Default` | `number` | Starting value |
| `Decimals` | `number` | Step precision (e.g. `0.01` for 2 decimal places, `1` for integers) |
| `Suffix` | `string` | Unit label displayed next to the value (e.g. `"px"`, `"s"`) |
| `Callback` | `function` | Called with new value on change |

#### Methods

```lua
Slider:Set(50)              -- Set value programmatically
Slider:Get()                -- Returns current number value
Slider:SetVisibility(false) -- Show or hide the slider
```

---

### Dropdown

A select menu for choosing one or multiple options.

```lua
local Dropdown = MySection:Dropdown({
    Name = "Mode",
    Flag = "ModeFlag",
    Items = { "Burst", "Auto", "Single" },
    Default = "Burst",
    Multi = false,
    Callback = function(Value)
        print(Value) -- string (or table if Multi = true)
    end
})
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | Label shown above the dropdown |
| `Flag` | `string` | Unique ID for this value |
| `Items` | `table` | Array of option strings |
| `Default` | `string` | Default selected option |
| `Multi` | `bool` | Allow multiple selections |
| `Callback` | `function` | Called with selected value(s) on change |

#### Methods

```lua
Dropdown:Set("Auto")                     -- Set selected option
Dropdown:Get()                           -- Returns current value
Dropdown:Add("NewOption")                -- Add a new option
Dropdown:Remove("Burst")                 -- Remove an option
Dropdown:Refresh({"A","B","C"}, true)    -- Replace all options (2nd arg = reset selection)
Dropdown:SetVisibility(false)            -- Show or hide
```

---

### Button

A row of one or more clickable buttons inside a section.

```lua
local Button = MySection:Button()

Button:Add("Apply", function()
    print("Apply clicked")
end)

Button:Add("Reset", function()
    print("Reset clicked")
end)
```

> Call `Button:Add()` once per button you want to add. Multiple calls create multiple buttons in the same row.

| Argument | Type | Description |
|---|---|---|
| `label` | `string` | The text shown on the button |
| `callback` | `function` | Called when the button is clicked |

---

### Colorpicker

A color picker that attaches **to an existing Toggle**. Call it as a method on a `Toggle` instance.

```lua
local Toggle = MySection:Toggle({
    Name = "FoV Circle",
    Flag = "FoVEnabled",
    Default = false,
    Callback = function(Value) end
})

Toggle:Colorpicker({
    Name = "FoV Color",
    Flag = "FoVColor",
    Default = Library.Theme.Accent, -- or Color3.fromRGB(255, 0, 0)
    Alpha = 0,
    Callback = function(Value)
        print(Value) -- Color3
    end
})
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | Label for the color picker |
| `Flag` | `string` | Unique ID for this color value |
| `Default` | `Color3` | Default color |
| `Alpha` | `number` | Starting alpha/transparency (`0`–`1`) |
| `Callback` | `function` | Called with `Color3` on change |

> You can attach **multiple** colorpickers to one toggle (e.g. fill color + outline color).

#### Methods

```lua
Colorpicker:Set(Color3.fromRGB(255, 0, 0))        -- Set color
Colorpicker:Set(Color3.fromRGB(255, 0, 0), 0.5)   -- Set color + alpha
Colorpicker:Get()                                  -- Returns Color3
Colorpicker:SetVisibility(false)                   -- Show or hide
Colorpicker:SlidePalette(InputObject)
Colorpicker:SlideHue(InputObject)
Colorpicker:SlideAlpha(InputObject)
Colorpicker:Update(bool, bool)
```

---

### Keybind

A keybind picker that attaches **to an existing Toggle**. Appears inline next to the toggle.

```lua
MySection:Toggle({
    Name = "Aimbot",
    Flag = "AimbotEnabled",
    Default = false,
    Callback = function(Value) end
}):Keybind({
    Flag = "AimbotKeybind",
    Default = Enum.KeyCode.E,
    Mode = "Toggle",
    Callback = function(Value)
        print(Value)
    end
})
```

| Property | Type | Description |
|---|---|---|
| `Flag` | `string` | Unique ID for this keybind |
| `Default` | `Enum.KeyCode` | Default key |
| `Mode` | `string` | `"Toggle"` or `"Hold"` |
| `Callback` | `function` | Called when key is pressed |

#### Methods

```lua
Keybind:Get()              -- Returns Key, Mode, Toggled
Keybind:Set(Enum.KeyCode.F) -- Set a new key
Keybind:SetMode("Hold")    -- Change mode
Keybind:Press()            -- Simulate a key press
```

---

### Searchbox

A dropdown with a live search/filter input. Useful for large item lists.

```lua
local Searchbox = MySection:Searchbox({
    Name = "Target Bone",
    Flag = "SearchboxFlag",
    Items = { "Head", "Torso", "LeftArm", "RightArm", "LeftLeg", "RightLeg" },
    Default = "Head",
    Multi = false,
    Callback = function(Value)
        print(Value)
    end
})
```

| Property | Type | Description |
|---|---|---|
| `Name` | `string` | Label shown above the searchbox |
| `Flag` | `string` | Unique ID for this value |
| `Items` | `table` | Array of option strings |
| `Default` | `string` | Default selected option |
| `Multi` | `bool` | Allow multiple selections |
| `Callback` | `function` | Called with selected value on change |

#### Methods

```lua
Searchbox:Set("Torso")                  -- Set selected option
Searchbox:Get()                         -- Returns current value
Searchbox:Add("Neck")                   -- Add an option
Searchbox:Remove("Head")                -- Remove an option
Searchbox:Refresh({"A","B"}, true)      -- Replace options
Searchbox:SetVisibility(false)          -- Show or hide
```

---

### Playerlist

A live player list component for a page. Callbacks fire when a player is selected or interacted with.

```lua
local Playerlist = PlayersPage:Playerlist({
    Callback = function(...)
        local Args = { ... }
        table.foreach(Args, print)
    end
})
```

---

## Notifications

Display a temporary toast notification on screen.

```lua
Library:Notification("Title", "Description text here", 5)
```

| Argument | Type | Description |
|---|---|---|
| `title` | `string` | Bold header of the notification |
| `description` | `string` | Body text |
| `duration` | `number` | Seconds before the notification disappears |

#### Example — Load Time Notification

```lua
local LoadTick = os.clock()

-- ... your UI setup code ...

Library:Notification(
    "Loaded!",
    "Menu took " .. string.format("%.4f", os.clock() - LoadTick) .. " seconds to load",
    5
)
```

---

## Full Boilerplate

A minimal starting template with one page, one section, and a few components.

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/QM80/APEX-UI-LIB/refs/heads/main/main/src/uilibrary.luau"))()

-- Window
local Window = Library:Window({
    FadeTime = 0.3,
    UITitle = "My Script",
})

-- Watermark & Keybinds
local Watermark = Library:Watermark("Loading...")
local KeybindList = Library:KeybindList()

-- Settings page (built-in)
local SettingsPage = Library:CreateSettingsPage(Window, Watermark, KeybindList)

-- Pages
local MainPage = Window:Page({ Name = "Main", Columns = 2 })

-- Sections
local MainSection = MainPage:Section({ Name = "General", Side = 1 })

-- Components
MainSection:Toggle({
    Name = "My Feature",
    Flag = "MyFeatureEnabled",
    Default = false,
    Callback = function(Value)
        print("My Feature:", Value)
    end
})

MainSection:Slider({
    Name = "Speed",
    Flag = "MyFeatureSpeed",
    Min = 0, Max = 100, Default = 16,
    Decimals = 1, Suffix = "studs/s",
    Callback = function(Value)
        print("Speed:", Value)
    end
})

-- Notification on load
local LoadTick = os.clock()
Library:Notification("Loaded!", "Took " .. string.format("%.4f", os.clock() - LoadTick) .. "s", 5)
```

---

> **Tip:** Every `Flag` string must be **unique** across your entire script. Flags are used internally to save/load config values.

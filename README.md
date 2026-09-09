<div align="center">

# 👋 Hi, I'm Illusion

<a href="https://github.com/IllusionAC">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&duration=2800&pause=1000&color=00F0FF&background=0D111700&center=true&vCenter=true&multiline=false&width=620&height=45&lines=Roblox+Systems+%26+Game+Engine+Developer;Creator+of+InputActionSystem+%26+IllusionSignal;Writing+Clean+Luau+%26+Exploring+Low-Level+C;Making+Input+Management+%26+UI+Scaling+Easy" alt="Typing SVG" />
</a>

<p align="center">
  <a href="https://discord.com"><img src="https://img.shields.io/badge/DISCORD-@illuusion-5865F2?style=flat-square&logo=discord&logoColor=white" alt="Discord" /></a>
  <a href="https://create.roblox.com/store/asset/92059655869452/Illusions-InputActionSystem-Module"><img src="https://img.shields.io/badge/CREATOR_STORE-IIAS_MODULE-E2231A?style=flat-square&logo=roblox&logoColor=white" alt="Creator Store" /></a>
  <img src="https://img.shields.io/badge/LOCATION-NIGHT_CITY%2C_N--USA-00F0FF?style=flat-square&logo=target&logoColor=black" alt="Location" />
  <img src="https://img.shields.io/badge/FOCUS-SYSTEMS_%26_TOOLING-00FF66?style=flat-square" alt="Focus" />
</p>

<p align="center">
  <a href="#-featured-modules"><b>Featured Modules</b></a> •
  <a href="#-open-source-libraries"><b>All Libraries</b></a> •
  <a href="#-engine-tricks--luau-insights"><b>Engine Quirks & Insights</b></a> •
  <a href="#-tech-stack"><b>Tech Stack</b></a> •
  <a href="#-activity--stats"><b>Activity</b></a> •
  <a href="#-connect"><b>Get in Touch</b></a>
</p>

---

</div>

### 🛠️ About Me

I build open-source modules and systems for **Roblox / Luau**, alongside exploring **C** and low-level programming. 

Most of my libraries were born out of real frustration with existing tools while building games, whether that's making input management work seamlessly across PC, Mobile, and Console without spaghetti code, fixing blurry text scaling without relying on Roblox's unpredictable `TextScaled`, or building a signal module that doesn't waste allocations during heavy gameplay loops.

I care about:
- **Clean APIs**: If a module takes more than 5 minutes to understand, the API needs work.
- **Predictable Behavior**: Systems should behave identically regardless of frame rate, platform, or screen resolution.
- **Strict Typing**: Leveraging Luau's `--!strict` and `--!native` features for fast, bulletproof code.

---

## 🚀 Featured Modules

<div align="center">
  <img src="https://raw.githubusercontent.com/IllusionAC/Illusion-InputActionSystem/main/IIASLogo.png" alt="IIAS Logo" width="120" />
  <h3>🎮 Illusion's InputActionSystem (IIAS)</h3>
  <p><i>A unified input and keybind library designed for Roblox's InputActionSystem with full Gamepad and Mobile support.</i></p>

  <p>
    <a href="https://create.roblox.com/store/asset/92059655869452/Illusions-InputActionSystem-Module"><img src="https://img.shields.io/badge/ROBLOX_STORE-GET_MODULE-E2231A?style=flat-square&logo=roblox&logoColor=white" alt="Creator Store" /></a>
    <a href="https://github.com/IllusionAC/Illusion-InputActionSystem"><img src="https://img.shields.io/badge/GITHUB-SOURCE_CODE-181717?style=flat-square&logo=github&logoColor=white" alt="Source" /></a>
  </p>
</div>

<details open>
<summary><b>⚡ [Interactive] Why IIAS? & Real Code Example (Click to toggle)</b></summary>
<br />

Handling inputs across keyboard, controllers, and touchscreen buttons in Roblox usually leads to messy boilerplate. IIAS handles the binding, priority, modifiers, and mobile UI buttons under one clean API.

#### What it handles:
- **Hold & Toggle Binds**: Switch between continuous hold or toggle activation with one line.
- **Multi-Key & Modifier Chords**: Easily bind actions like `Shift + F` or multiple keys for one action.
- **Mobile UI Binding**: Pass a `GuiButton` directly to the bind, touch players get automatic synchronization.
- **Input Buffering & Cooldowns**: Built-in buffer window so inputs pressed during animations or busy frames aren't lost.
- **Double-Tap / Multi-Tap**: Native support for tap count and window intervals (e.g., double-tap W to dash).

#### Real Example: Sprint & Walk System
```luau
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local IIAS = require(ReplicatedStorage.IllusionIAS)

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Create binds
local sprint = IIAS.new("Sprint")
local walk = IIAS.new("Walk")

-- Configure as hold buttons
sprint:SetHold(true)
walk:SetHold(true)

-- Bind keyboard & controller triggers
sprint:AddBind(Enum.KeyCode.LeftShift)
walk:AddBind(Enum.KeyCode.LeftControl)

-- Connect cleanly to your movement logic
sprint.Activated:Connect(function(active, pressed)
    humanoid.WalkSpeed = active and 24 or 16
end)

walk.Activated:Connect(function(active, pressed)
    humanoid.WalkSpeed = active and 8 or 16
end)
```

</details>

---

<table width="100%">
<tr>
<td width="50%" valign="top">

### ⚡ [IllusionSignal](https://github.com/IllusionAC/IllusionSignal)
**Fast, lightweight custom event dispatcher in native Luau**

Designed specifically for `IIAS` to handle rapid event firing without unnecessary table reallocations.

<details>
<summary><b>🔍 Code & Under-The-Hood</b></summary>
<br />

Uses `--!strict`, `--!optimize 2`, and `--!native`. Connections are maintained via a **doubly-linked list** (`head`, `next`, `prev`), so connecting and disconnecting listeners never incurs the cost of table re-indexing (`table.remove`).

```luau
local Signal = require(path.to.IllusionSignal)

local onStateChanged = Signal.new()

local connection = onStateChanged:Connect(function(newState)
    print("State updated:", newState)
end)

onStateChanged:Fire("ACTIVE")
connection:Disconnect()
```

</details>

</td>
<td width="50%" valign="top">

### 📐 [Illusion ScaleManager](https://github.com/IllusionAC/Illusion-ScaleManager)
**Predictable UI text scaling without `TextScaled`**

`TextScaled` has always been frustrating: text gets blurry, scales unpredictably, and ignores layout hierarchy. 

<details>
<summary><b>🔍 Code & How it Works</b></summary>
<br />

ISM controls the text size by dynamically calculating a `UIScale` based on camera viewport width:
`scale = viewportWidth / baseWidth`

```luau
local ScaleManager = require(ReplicatedStorage.IllusionScaleManager)

local label = script.Parent:WaitForChild("TextLabel")

-- Base reference resolution (default 1920)
local scaledText = ScaleManager.new(label, 1920)

-- Cleans up automatically when the label is destroyed
```

</details>

</td>
</tr>
<tr>
<td width="50%" valign="top">

### 💾 [IllusionDSS](https://github.com/IllusionAC/IllusionDSS)
**Schema-agnostic DataStore module with backoff**

A clean wrapper for player data persistence where your schema stays strictly in your game scripts, not hardcoded into the module.

<details>
<summary><b>🔍 Code & Setup</b></summary>
<br />

```luau
local IllusionDSS = require(ServerStorage.IllusionDSS)

local Store = IllusionDSS.new(DataStoreService, Players, {
    DataStoreName = "PlayerData_V1",
    DefaultData = { Coins = 0, Wins = 0, Inventory = {} },
    KeyBuilder = function(player)
        return `player_{player.UserId}`
    end,
    MaxRetries = 5,
    RetryBaseSeconds = 2,
    AutoSaveOnLeave = true,
})

Players.PlayerAdded:Connect(function(player)
    Store:Load(player)
end)
```

</details>

</td>
<td width="50%" valign="top">

### 🔤 [Illusion-LiteralCount](https://github.com/IllusionAC/Illusion-LiteralCount)
**Number string to English word literal converter**

Parses numeric string values into grammatically correct English words without floating-point overflow limits.

<details>
<summary><b>🔍 Code & Grammar Rules</b></summary>
<br />

Handles decimals, fractions (`tenths`, `thousandths`), hyphenated tens, and massive integers:

```luau
local IL = require(path.to.IllusionLiteral)

print(IL.ToLiteral("4451"))
--> "four thousand four hundred and fifty-one"

print(IL.ToLiteral("12.34"))
--> "twelve and thirty-four hundredths"

print(IL.ToLiteral("44545611541174545117411414514545"))
--> huge values supported without IEEE-754 precision loss
```

</details>

</td>
</tr>
</table>

---

## 💡 Engine Quirks & Luau Insights

<p><i>Real tricks and architectural choices extracted from working code in my repositories:</i></p>

<details>
<summary><b>1. Cleanly Stopping Roblox's Default Character Health Script</b></summary>
<br />

> In standard Roblox games, if you delete `Player.Character.Health` at runtime, the script has already started executing in a new thread.
> 
> In [Illusion-HealthScript](https://github.com/IllusionAC/Illusion-HealthScript), we inject a placeholder before the character spawns:
> ```luau
> local replace = Instance.new("IntValue")
> replace.Name = "Health"
> replace.Parent = game:GetService("StarterPlayer").StarterCharacterScripts
> ```
> When the character spawns, the engine detects that an instance named `"Health"` already exists in `StarterCharacterScripts` and **skips generating the default regeneration script entirely**. A single server controller can then handle all regeneration cleanly.

</details>

<details>
<summary><b>2. Why Doubly-Linked Lists Beat Array Tables for Signals</b></summary>
<br />

> Most simple Signal implementations in Luau store callbacks in an array table and call `table.remove(list, idx)` or `table.find()` on `:Disconnect()`. 
> 
> In high-frequency input loops (like mouse movement or button taps), frequent connects/disconnects cause array shifts and memory reallocation. In [IllusionSignal](https://github.com/IllusionAC/IllusionSignal), every connection node simply holds pointers to `next` and `prev`. Disconnecting is a constant-time $O(1)$ pointer update with zero table resizing.

</details>

<details>
<summary><b>3. Strict-Typed OOP in Luau Without Metatable Overhead</b></summary>
<br />

> In [Illusion-OOPWay](https://github.com/IllusionAC/Illusion-OOPWay), I demonstrate writing OOP code that gives the Luau type checker 100% visibility without metatable typecasting hacks:
> ```luau
> export type Object = {
>     attribute: boolean,
>     setAttribute: (self: Object, state: boolean) -> (),
>     getAttribute: (self: Object) -> boolean,
> }
> 
> function Module.new(): Object
>     local self: Object = {
>         attribute = true,
>         setAttribute = function() end,
>         getAttribute = function() return false end,
>     }
>     function self:setAttribute(state: boolean)
>         self.attribute = state
>     end
>     function self:getAttribute()
>         return self.attribute
>     end
>     return self
> end
> ```
> IDE autocomplete is instant, and every method signature is strictly enforced.

</details>

---

## 📂 Open-Source Libraries

| Module / Project | Description | Links |
| :--- | :--- | :--- |
| [**Illusion-InputActionSystem**](https://github.com/IllusionAC/Illusion-InputActionSystem) | Full-featured Roblox keybind, gamepad, and touch input system | [GitHub](https://github.com/IllusionAC/Illusion-InputActionSystem) • [Creator Store](https://create.roblox.com/store/asset/92059655869452/Illusions-InputActionSystem-Module) |
| [**IllusionSignal**](https://github.com/IllusionAC/IllusionSignal) | High-performance, linked-list-based custom signal module in native Luau | [GitHub](https://github.com/IllusionAC/IllusionSignal) |
| [**Illusion-ScaleManager**](https://github.com/IllusionAC/Illusion-ScaleManager) | Viewport-width UI text scaler that replaces `TextScaled` | [GitHub](https://github.com/IllusionAC/Illusion-ScaleManager) |
| [**IllusionDSS**](https://github.com/IllusionAC/IllusionDSS) | Reusable, schema-agnostic DataStore wrapper with retry backoff | [GitHub](https://github.com/IllusionAC/IllusionDSS) |
| [**Illusion-LiteralCount**](https://github.com/IllusionAC/Illusion-LiteralCount) | Converts numeric string values to literal English words with grammar rules | [GitHub](https://github.com/IllusionAC/Illusion-LiteralCount) |
| [**Illusion-CountdownSystem**](https://github.com/IllusionAC/Illusion-CountdownSystem) | Clean server-authoritative countdown system with client sync | [GitHub](https://github.com/IllusionAC/Illusion-CountdownSystem) |
| [**Illusion-HealthScript**](https://github.com/IllusionAC/Illusion-HealthScript) | Centralized character health regeneration replacing the default Roblox script | [GitHub](https://github.com/IllusionAC/Illusion-HealthScript) |
| [**Illusion-OOPWay**](https://github.com/IllusionAC/Illusion-OOPWay) | Idiomatic pattern for strict-typed OOP in Luau | [GitHub](https://github.com/IllusionAC/Illusion-OOPWay) |

---

## 🧰 Tech Stack

<div align="center">

```
  Game Systems & Runtime   :  Luau  •  Roblox Engine  •  Rojo  •  Roblox Studio
  Systems & Low-Level      :  C  •  C++20  •  Memory Management  •  Data Structures
  Tooling & Workflow       :  Git  •  GitHub Actions  •  VS Code  •  PowerShell
```

</div>

---

## 📊 Activity & Stats

<div align="center">

<img src="https://github-readme-stats.vercel.app/api?username=IllusionAC&show_icons=true&title_color=00F0FF&text_color=C9D1D9&icon_color=00FF66&bg_color=0D1117&border_color=30363D&hide_border=false" alt="IllusionAC GitHub Stats" width="48%" />
&nbsp;
<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=IllusionAC&layout=compact&title_color=00F0FF&text_color=C9D1D9&bg_color=0D1117&border_color=30363D&hide_border=false" alt="Top Languages" width="48%" />

</div>

---

## 📬 Connect

If you're using **IIAS**, have a suggestion for one of the modules, or want to talk game engine architecture:

- **Discord**: `@illuusion`
- **GitHub Issues**: Feel free to open an issue or discussion on any of the public repos
- **Roblox Creator Store**: [Illusion's InputActionSystem Asset](https://create.roblox.com/store/asset/92059655869452/Illusions-InputActionSystem-Module)

<div align="center">
<sub>Illusion • Night City, N-USA</sub>
</div>

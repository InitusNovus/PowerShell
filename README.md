# PowerShell Profile

> [English](README.md) | [한국어](README.ko.md)

A reusable `pwsh` (PowerShell 7) profile setup for Windows-first development environments. Designed to be beginner-friendly while offering deep customization for power users.

## Table of Contents

1. [Quick Start](#1-quick-start)
2. [Screenshots / Output Examples](#2-screenshots--output-examples)
3. [Requirements](#3-requirements)
4. [Installation](#4-installation)
5. [Features](#5-features)
6. [Configuration](#6-configuration)
7. [Built-in Commands](#7-built-in-commands)
8. [Workflow Examples](#8-workflow-examples)
9. [Oh My Posh Theme](#9-oh-my-posh-theme)
10. [VS Code Integration](#10-vs-code-integration)
11. [Troubleshooting](#11-troubleshooting)
12. [Repository Layout](#12-repository-layout)
13. [Git / Publishing Model](#13-git--publishing-model)
14. [Notes](#14-notes)

---

## 1. Quick Start

30 seconds to a working setup. Copy-paste each line.

### Step 1: Install PowerShell 7

Download from Microsoft or use winget:

```powershell
winget install Microsoft.PowerShell
```

### Step 2: Clone the repository into your profile directory

```powershell
# Find your profile directory
Split-Path $PROFILE -Parent
# Clone into it (replace with your actual path)
git clone https://github.com/YOUR_USERNAME/PowerShell "C:\Users\YOUR_NAME\Documents\PowerShell"
```

### Step 3: Create local configuration

```powershell
# In the cloned directory
Copy-Item .\config.template.psd1 .\config.local.psd1
```

### Step 4: Start a new PowerShell session

```powershell
pwsh
```

### Step 5: Verify it works

```powershell
phelp
```

You should see the help menu. If the prompt shows boxes or question marks, see [Troubleshooting](#11-troubleshooting) for the Nerd Font fix.

---

## 2. Screenshots / Output Examples

Here is what you will see when everything is working.

### Startup Banner

When you open a new terminal, the profile automatically displays a status banner:

```
======================================================================
[PowerShell profile] 2026-04-09 18:30:00
----------------------------------------------------------------------
[Python] python  : C:\Users\user\dev\_global-py\.venv\Scripts\python.exe (v3.12.4)
[Python] venv    : _global-py (C:\Users\user\dev\_global-py\.venv)
[uv]     managed : ALLOWED
[uv]     python  : C:\Users\user\dev\_global-py\.venv\Scripts\python.exe (v3.12.4)
[MSYS2]  status  : preferred=UCRT64 | active=UCRT64 | ucrt64[2] mingw64[not-in-path]
[Help]   phelp   : phelp (use -a for all; -d/-s/-c for sections)
======================================================================
```

### Help Menu (`phelp`)

```
=== PowerShell profile help ===
Usage:
  phelp [ -d | -s | -c | -a ]
  phelp [ --devtools | --shortcuts | --profilecommands | --all ]
```

### Dev Tools Check (`Test-DevTools`)

```
=== Dev tool status ===
[oh-my-posh] C:\Users\user\AppData\Local\Programs\OhMyPosh\bin\oh-my-posh.exe (v3.0.0)
[git]        C:\Program Files\Git\cmd\git.exe (git version 2.45.0.windows.1)
[python]     Python 3.12.4
[pip]        pip 24.0 from C:\Users\user\dev\_global-py\.venv\Scripts\pip.exe
[uv]         uv 0.4.0
[gcc]        gcc (x86_64-posix-seh, built by MSYS2) 14.2.0
[g++]        g++ (x86_64-posix-seh, built by MSYS2) 14.2.0
[cmake]      C:\msys64\ucrt64\bin\cmake.exe (cmake version 3.29.2)
[ninja]      C:\msys64\ucrt64\bin\ninja.exe (v1.12.1)
```

---

## 3. Requirements

### PowerShell 7+ (`pwsh`) — Required

PowerShell 7 is the core runtime. This profile targets `pwsh` (version 7 or higher), not the legacy Windows PowerShell 5.1.

**Why needed**: This profile uses PowerShell 7 features and syntax that are not available in PowerShell 5.1. Many helper functions also depend on cross-platform compatibility (`$IsWindows`, `$HOME`, etc.).

**Install via winget**:

```powershell
winget install Microsoft.PowerShell
```

**Verify**:

```powershell
pwsh --version
```

### Oh My Posh — Required

Oh My Posh renders the styled prompt with icons, git status, and path-aware coloring. The profile includes a hardcoded theme (`theme/clean-detailed_custom.omp.json`) and exports `POSH_*` environment variables for dynamic path icon mapping. Without Oh My Posh, the profile still loads but you lose the core prompt experience.

**What it does**: Replaces the plain PowerShell prompt with a themeable, feature-rich prompt that shows directory context, git branch, error states, and execution time.

**Install via winget**:

```powershell
winget install JanDeDobbeleer.OhMyPosh
```

**Verify**:

```powershell
oh-my-posh version
```

[Oh My Posh documentation](https://ohmyposh.dev/docs/installation/windows)

### Nerd Font — Required (with Oh My Posh)

The included Oh My Posh theme uses glyphs and icons from Nerd Fonts (folder icons, git symbols, language logos, path icons). Without a Nerd Font installed and selected as your terminal font, these glyphs appear as `□` or `?` boxes.

**Recommended fonts**: `CascadiaCode Nerd Font` or `FiraCode Nerd Font` — both work well with this profile.

**Install**:

```powershell
# Option 1: Oh My Posh built-in font installer (recommended)
oh-my-posh font install CascadiaCode

# Option 2: Download manually from nerdfonts.com
```

**Set in terminal**: After installing, go to your terminal settings and select the Nerd Font variant (e.g., `CascadiaCode Nerd Font`) as the active font.

[Nerd Fonts downloads](https://www.nerdfonts.com/font-downloads)

### `uv` — Optional

`uv` is a fast Python package manager and environment tool from Astral. The profile uses it for global venv management and managed Python policy.

**What it is**: A drop-in replacement for `pip` + `virtualenv` that is 10-100x faster. Used here for the global Python environment activation/deactivation workflow.

**Install via winget**:

```powershell
winget install Astral.uv
```

[uv documentation](https://docs.astral.sh/uv/)

### MSYS2 — Optional

MSYS2 provides GCC, G++, CMake, and Ninja toolchains on Windows. The profile can switch between `UCRT64` and `MINGW64` toolchains.

**What it is**: A Linux-style build environment for Windows with GCC/MinGW-w64 toolchains. Essential for compiling C/C++ projects or building software with CMake.

**Install**: Download from [msys2.org](https://www.msys2.org/) and run the installer.

**Note**: After installing, add your MSYS2 path to `config.local.psd1`:

```powershell
@{ Msys2Root = "C:\msys64" }
```

### VS Code — Optional

If you use Visual Studio Code, the profile includes a dedicated wrapper that loads the same profile in the VS Code terminal.

**What it does**: `Microsoft.VSCode_profile.ps1` dot-sources the main profile, so your VS Code terminal gets the same prompts, shortcuts, and commands.

[VS Code](https://code.visualstudio.com/)

---

## 4. Installation

### Option 1 — Direct clone into profile directory

The simplest setup. The repository becomes your PowerShell profile directory.

```powershell
# Step 1: Find your profile directory
Split-Path $PROFILE -Parent
# Result: C:\Users\YOUR_NAME\Documents\PowerShell

# Step 2: Clone into it (replace the path below)
git clone https://github.com/YOUR_USERNAME/PowerShell "C:\Users\YOUR_NAME\Documents\PowerShell"

# Step 3: Create local config
Copy-Item .\config.template.psd1 .\config.local.psd1

# Step 4: Open a new terminal
pwsh
```

If the repository lives directly in your PowerShell profile directory, `Microsoft.PowerShell_profile.ps1` and `Microsoft.VSCode_profile.ps1` load automatically when you start `pwsh`.

### Option 2 — Symlink from another location

Keep the repository in a custom location and create symlinks to the profile directory.

**Requires elevated (admin) terminal**:

```powershell
# Create symlinks (run as Administrator)
New-Item -ItemType SymbolicLink -Path "C:\Users\YOUR_NAME\Documents\PowerShell\Microsoft.PowerShell_profile.ps1" -Target "C:\path\to\repo\Microsoft.PowerShell_profile.ps1"
New-Item -ItemType SymbolicLink -Path "C:\Users\YOUR_NAME\Documents\PowerShell\Microsoft.VSCode_profile.ps1" -Target "C:\path\to\repo\Microsoft.VSCode_profile.ps1"
```

The profile resolves its config and theme paths relative to the script location, so symlinked entrypoints work correctly.

### Verify installation

```powershell
phelp          # Should show the help menu
Test-DevTools  # Should list detected tools with versions
```

---

## 5. Features

### Prompt and Shell UX

- Oh My Posh prompt with a custom theme loaded from `theme/clean-detailed_custom.omp.json`
- Path icon mapping driven by exported `POSH_*` environment variables (e.g., `POSH_DESKTOP`, `POSH_DEV`, `POSH_WORKSPACE`)
- Startup banner showing Python version, venv status, uv managed Python policy, and MSYS2 toolchain state
- Console-width aware layout that adapts to narrow terminals

### Folder Shortcuts

The profile creates PowerShell variables for quick navigation:

| Variable | Aliases | Path |
|----------|---------|------|
| `$DESKTOP` | — | System Desktop folder |
| `$DEV` | — | `~/dev` by default |
| `$WORKSPACE` | `$WS` | `~/Desktop/Workspace` by default |
| `$OPENCODE` | `$OC` | `~/.config/opencode` by default |
| `$GOOGLEDRIVE` | `$GDRIVE`, `$GD` | Only when set in `config.local.psd1` |

These shortcuts make it easy to jump to project folders without typing full paths.

### Python and `uv` Helpers

- **Global venv activation/deactivation**: `Activate-UvGlobal` (`ga`) and `Deactivate-UvGlobal` (`gde`) switch the global Python environment
- **Python path inspection**: `Get-PythonExecutable` (`pywhere`, `pyw`) shows the active python path
- **uv managed Python policy**: `Enable-UvManagedPythonBlock` (`uvblock`) and `Disable-UvManagedPythonBlock` (`uvallow`) control whether `uv` can install its own managed Python
- **Environment info**: `Get-PythonEnvInfo` (`pyinfo`, `pyi`) shows prefix, base prefix, and venv detection

### MSYS2 Toolchain Switching

Switch between UCRT64 and MINGW64 toolchains on Windows:

| Command | Description |
|---------|-------------|
| `Use-UCRT64` | Prefer the UCRT64 toolchain (modern, recommended) |
| `Use-MINGW64` | Prefer the MINGW64 toolchain (legacy projects) |
| `Show-Msys2Toolchain` | Show current toolchain status, PATH order, and detected compilers |
| `Reset-Msys2Toolchain` | Reset to UCRT64 as the default |

### Built-in Help and Diagnostics

- `phelp` (`ph`) — help menu with `-d`, `-s`, `-c`, `-a` options for targeted views
- `Test-DevTools` — show external tool paths and versions (git, python, pip, uv, gcc, g++, cmake, ninja)
- `Show-ShortcutPaths` — display all shortcut variable values
- `Show-ProfileCommands` — list every profile command organized by category
- `Show-StartupBanner -Force` — redraw the banner (useful after changes)

### PATH Utilities

The profile maintains a session baseline for PATH and provides utilities to modify it safely:

- `Get-PathEntries` — list all PATH entries
- `Remove-PathEntry "path"` — remove a specific path from PATH
- `Prepend-PathEntry "path"` — move a path to the front of PATH
- `Reset-Path` — restore PATH to the session start state
- `Rebaseline-Path` — update the stored baseline to the current PATH

---

## 6. Configuration

The profile uses two config files:

- `config.template.psd1` — tracked shared defaults (do not edit directly)
- `config.local.psd1` — machine-specific overrides, ignored by Git

Create your local config:

```powershell
Copy-Item .\config.template.psd1 .\config.local.psd1
```

### Configuration Keys

| Key | Purpose | Default | Path Relative To |
|-----|---------|---------|-----------------|
| `OhMyPoshTheme` | Oh My Posh theme file path | `theme/clean-detailed_custom.omp.json` | Profile directory |
| `UvGlobalVenv` | Global uv venv location | `dev/_global-py/.venv` | `$HOME` |
| `DevFolder` | Development folder name | `dev` | `$HOME` |
| `DesktopFolder` | Desktop folder name | `Desktop` | System API (`$HOME` fallback) |
| `WorkspaceFolder` | Workspace subfolder | `Desktop\Workspace` | `$HOME` |
| `OpenCodeConfigFolder` | OpenCode config folder | `.config\opencode` | `$HOME` |
| `GoogleDrive` | Google Drive root path | (not set) | `$HOME` (local only) |
| `Msys2Root` | MSYS2 installation path | (not set) | Absolute path (local only) |

### Example `config.local.psd1`

```powershell
@{
    GoogleDrive = "D:\YourDrive"
    Msys2Root   = "C:\msys64"
}
```

Only include keys you actually need. Omitting a key uses the default from the template.

---

## 7. Built-in Commands

### Help and Diagnostics

| Command | Aliases | Description |
|---------|---------|-------------|
| `Show-ProfileHelp` | `phelp`, `ph`, `profilehelp` | Show help menu with options |
| `Test-DevTools` | — | Show external tool paths and versions |
| `Show-ShortcutPaths` | — | Show shortcut variable paths |
| `Show-ProfileCommands` | — | List all profile commands by category |
| `Show-StartupBanner` | — | Re-render the startup banner |

### PATH Management

| Command | Aliases | Description |
|---------|---------|-------------|
| `Get-PathEntries` | — | List all PATH entries (internal use) |
| `Remove-PathEntry` | — | Remove a path from current session PATH |
| `Prepend-PathEntry` | — | Move a path to the front of PATH |
| `Reset-Path` | — | Restore PATH to session start state |
| `Rebaseline-Path` | — | Update PATH baseline to current state |

### Python and `uv`

| Command | Aliases | Description |
|---------|---------|-------------|
| `Activate-UvGlobal` | `ga` | Activate the configured global uv venv |
| `Deactivate-UvGlobal` | `gde` | Deactivate the global uv venv |
| `Get-PythonExecutable` | `pywhere`, `pyw` | Show active python executable path |
| `Get-PythonEnvInfo` | `pyinfo`, `pyi` | Show detailed Python environment info |
| `Get-UvPythonExecutable` | `pyuvinfo`, `pyuv`, `uvi` | Show uv-managed python path |
| `Get-PythonPath` | `pypath`, `pyp` | Resolve python from PATH (fast, Get-Command based) |
| `Get-PipExecutable` | `pipwhere`, `pipw` | Show active pip executable path |
| `Get-UvPipExecutable` | `pipuv`, `uvpip` | Show uv-managed pip path |
| `Get-PipPath` | `pippath`, `pipp` | Resolve pip from PATH (fast, Get-Command based) |

### `uv` Managed Python Policy

| Command | Aliases | Description |
|---------|---------|-------------|
| `Get-UvManagedPythonPolicy` | `uvpolicy`, `uvp` | Show current managed Python policy (ALLOWED/BLOCKED) |
| `Enable-UvManagedPythonBlock` | `uvblock`, `uvb` | Block uv managed Python for this session |
| `Disable-UvManagedPythonBlock` | `uvallow`, `uva` | Allow uv managed Python for this session |
| `Invoke-UvWithManagedPython` | `uvm` | Run uv with managed Python temporarily enabled |

### MSYS2 Toolchain (Windows only)

| Command | Aliases | Description |
|---------|---------|-------------|
| `Show-Msys2Toolchain` | — | Show detailed MSYS2 toolchain status, PATH order, and detected tools |
| `Use-UCRT64` | — | Switch to UCRT64 toolchain (default, modern) |
| `Use-MINGW64` | — | Switch to MINGW64 toolchain (legacy projects) |
| `Reset-Msys2Toolchain` | — | Reset toolchain preference to UCRT64 |

---

## 8. Workflow Examples

### Python Workflow with `uv`

```powershell
# Activate the global venv before working
ga

# ... do your Python work ...

# Deactivate when done
gde
```

The global venv is shared across all projects. Use per-project venvs with `uv venv` for isolated project environments.

### MSYS2 Toolchain Switching

```powershell
# For modern C++ projects (recommended)
Use-UCRT64

# For legacy projects that need MINGW64
Use-MINGW64

# Check current status
Show-Msys2Toolchain

# Reset to default (UCRT64)
Reset-Msys2Toolchain
```

### PATH Troubleshooting

```powershell
# View all PATH entries (numbered)
Get-PathEntries

# Remove a bad entry
Remove-PathEntry "C:\OldPython39"

# Reset PATH to session start if things get messed up
Reset-Path

# Save the current state as the new baseline
Rebaseline-Path
```

### First-time Setup Check

```powershell
# See everything at once
phelp -a

# Check which dev tools are installed
Test-DevTools

# See your shortcut paths
Show-ShortcutPaths
```

---

## 9. Oh My Posh Theme

The active theme is at:

```
theme/clean-detailed_custom.omp.json
```

Before Oh My Posh initializes, the profile exports `POSH_*` environment variables for path-aware icon mapping:

- `POSH_DESKTOP`
- `POSH_DOCUMENTS`
- `POSH_DOWNLOADS`
- `POSH_DEV`
- `POSH_WORKSPACE`
- `POSH_OPENCODE`
- `POSH_GOOGLEDRIVE`

The theme uses these in `mapped_locations` so icons update dynamically based on your folder layout, without hardcoding machine-specific paths.

### Customizing the Theme

**Option 1 — Edit the JSON directly**:

Open `theme/clean-detailed_custom.omp.json` and modify segments, colors, or glyphs.

**Option 2 — Switch to a different theme**:

1. Download or create a new `.omp.json` theme
2. Place it in the `theme/` directory
3. Update `OhMyPoshTheme` in `config.local.psd1`:

```powershell
@{ OhMyPoshTheme = "theme/my-custom-theme.omp.json" }
```

Older theme snapshots are preserved in `theme/archive/` for reference.

---

## 10. VS Code Integration

`Microsoft.VSCode_profile.ps1` is a thin wrapper that dot-sources the main profile. When you open the terminal in VS Code, this wrapper loads instead of the main profile.

This means:
- Your VS Code terminal gets the same prompts, shortcuts, and commands
- Oh My Posh loads inside VS Code with shell integration enabled
- Folder shortcuts (`$DEV`, `$WORKSPACE`, etc.) are available in the VS Code terminal

### VS Code Settings

Use `.vscode/settings.template.json` as a reference for workspace-local settings. Your actual `.vscode/settings.json` is ignored by Git (listed in `.gitignore`), so personal settings stay local.

---

## 11. Troubleshooting

### Prompt shows `□` or `?` boxes

**Cause**: Missing Nerd Font.

**Fix**: Install a Nerd Font (e.g., `CascadiaCode Nerd Font`) from [nerdfonts.com](https://www.nerdfonts.com/font-downloads) and select it in your terminal settings.

### Banner doesn't appear on startup

**Cause**: Banner shows only once per session by default.

**Fix**: Run `Show-StartupBanner -Force` to redraw it.

### `phelp` not found after installation

**Cause**: Profile not loaded, possibly wrong `$PROFILE` path.

**Fix**:

```powershell
# Check the profile path
$PROFILE

# Verify the file exists there
Test-Path $PROFILE

# If not, close and reopen the terminal
```

### Python not detected

**Cause**: Python not in PATH, or neither Python nor `uv` is installed.

**Fix**: Install Python or `uv`:

```powershell
winget install Python.Python.3.12
# or
winget install Astral.uv
```

Then restart the terminal.

### MSYS2 commands not working

**Cause**: `Msys2Root` not configured in `config.local.psd1`.

**Fix**: Add your MSYS2 path:

```powershell
@{ Msys2Root = "C:\msys64" }
```

Then restart the terminal.

### Oh My Posh not loading

**Cause**: Oh My Posh not installed.

**Fix**:

```powershell
winget install JanDeDobbeleer.OhMyPosh
```

Then restart the terminal.

### PATH feels wrong after experimenting

**Cause**: Direct PATH manipulation can leave duplicates or wrong ordering.

**Fix**:

```powershell
Reset-Path  # Restores PATH to session start
```

To save the current state as the baseline:

```powershell
Rebaseline-Path
```

---

## 12. Repository Layout

```
.
├─ Microsoft.PowerShell_profile.ps1   # Main profile entrypoint
├─ Microsoft.VSCode_profile.ps1       # VS Code terminal wrapper
├─ README.md
├─ config.template.psd1              # Tracked shared defaults
├─ config.local.psd1                  # Local overrides (gitignored)
├─ .gitignore
├─ theme/
│  ├─ clean-detailed_custom.omp.json  # Active Oh My Posh theme
│  └─ archive/                        # Older theme snapshots
├─ docs/
│  └─ PowerShell_Profile_Analysis.md  # Design notes and analysis
└─ .vscode/
   ├─ settings.template.json          # VS Code workspace template
   └─ settings.json                  # Local VS Code settings (gitignored)
```

---

## 13. Git / Publishing Model

This repository is designed so that:

- Shared profile logic is tracked in Git
- Local machine paths stay in `config.local.psd1`
- Local editor state stays in `.vscode/settings.json`
- Theme history is preserved under `theme/archive/`

If you plan to publish your own fork publicly, review your local overrides and Git history before pushing.

If your existing local git history already contains personal paths, old local configs, or personal author metadata, the cleanest public-release path is usually to start with a fresh repository history after sanitizing the working tree.

---

## 14. Notes

- This profile is Windows-first, but several sections are intentionally cross-platform (PATH utilities, Python helpers, Oh My Posh init).
- If `Oh My Posh`, `uv`, or MSYS2 are missing, the profile still loads. Related commands become partial or informational.
- If you change local folder paths, restart your shell so the exported `POSH_*` variables and prompt mappings update together.
- For manual testing, open a fresh terminal instead of repeatedly dot-sourcing the profile during automation-heavy sessions.
- The source code comments are in Korean. This is intentional to help Korean-speaking developers understand and modify the profile behavior.

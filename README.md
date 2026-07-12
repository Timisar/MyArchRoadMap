#Disclamer!!!

Still in beta testing. Trying to release a Gui based support to in QML in future.

# winpkg – Wine/Proton app & game manager

> One‑line setup for Windows apps/games on Arch/CachyOS.  
> Uses **reflinks** (Btrfs) to share a single pre‑baked Wine prefix – pay the download cost **once**, create any number of apps instantly.

---

## Quickstart

```bash
# 1. Install the script (make executable)
chmod +x winpkg
sudo mv winpkg /usr/local/bin/   # or keep it anywhere in PATH

# 2. Build the shared template (one‑time, downloads VC++/.NET/etc.)
winpkg init

# 3. Build your app/game (everything in one go)
winpkg build game RDR ~/Downloads/setup.exe vcrun2022   # for games (uses umu‑run)
# or
winpkg build app Office ~/Downloads/OfficeSetup.exe      # for apps (uses wine)

# 4. Launch
winpkg run game RDR           # terminal
# or click the "RDR" icon in your system menu
```

---

## What it does

- Creates isolated prefixes under `~/.local/share/winpkg/`
- Apps (`app`) use `wine`; games (`game`) use `umu‑run` (Proton)
- Automatically detects the main `.exe` and creates a `.desktop` launcher
- Snapshots every install/deps change – rollback any time
- Manages dependencies (winetricks verbs) per app

---

## Commands (the ones you’ll use)

| Command | Purpose |
|---------|---------|
| `winpkg init` | Build shared template (VC++ 2005–2022, .NET 3.5/4.8/6/7/8, corefonts) – run once |
| `winpkg build app\|game NAME INSTALLER [DEPS...]` | Full build: create prefix, install deps, run installer, create menu entry |
| `winpkg run app\|game NAME` | Launch the app/game |
| `winpkg status` | Overview of all installed apps/games |
| `winpkg doctor app\|game NAME` | Check if everything is healthy |
| `winpkg remove app\|game NAME` | Move to trash (recoverable) |
| `winpkg info app\|game NAME` | Show full config and paths |
| `winpkg snapshot/restore app\|game NAME LABEL` | Save/restore a prefix state |

---

## Examples

### Build a game with extra deps
```bash
winpkg build game Cyberpunk ~/Downloads/setup.exe vcrun2022 dotnet48
```

### Build and pass installer arguments
```bash
winpkg build app Office ~/Downloads/setup.exe -- /quiet /norestart
```

### Manually set the executable (skip auto‑scan)
```bash
winpkg build game RDR ~/Downloads/setup.exe --exe "Program Files/RDR/RDR.exe"
```

### Use custom Proton (set once in your shell)
```bash
export PROTONPATH="$HOME/.local/share/Steam/compatibilitytools.d/cachyos-proton"
winpkg build game MyGame ~/Downloads/setup.exe
```

---

## Requirements

- Arch/CachyOS (or any distribution with `wine`, `winetricks`, `python3`, `curl`, `util‑linux`, `fontconfig`)
- **Btrfs** recommended for reflink (zero‑copy); falls back to full copy otherwise
- For games: `umu-launcher` (install with `yay -S umu-launcher`)

---

## Directory layout

```
~/.local/share/winpkg/
  template/             shared prefix (reflinked to all apps)
  apps/<name>/          each app’s prefix + config + snapshots
  games/<name>/         each game’s prefix + config + snapshots
  installers/           cached downloads
  logs/                 run logs
  trash/                soft‑deleted apps/games
```

---

## Rollback

Every `deps` or `install` creates an automatic snapshot.  
To restore:

```bash
winpkg snapshots game RDR          # list snapshots
winpkg restore game RDR pre-install-20260115-123456
```

---

## Uninstall

```bash
winpkg remove game RDR            # moves to trash (recoverable)
winpkg trash-empty                # permanently delete trash
```

---

## Full reference

All advanced commands (`create`, `deps`, `install`, `scan`, `menu`, `set‑runner`, `set‑env`, `repair`, `upgrade‑prefix`, `clone`, `snapshot‑remove`) are documented in the script’s `--help`:

```bash
winpkg --help
```

---

**That’s it.** Go build something. 

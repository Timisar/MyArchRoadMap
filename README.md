Just My Cachyos Projects:


# winpkg

A small tool for installing and running Windows apps/games on Arch/CachyOS via Wine, without hand-managing prefixes yourself.

## Setup (fish shell)

Assuming you are using fish shell like me:

```fish
mkdir -p ~/.local/bin
mv ~/Downloads/winpkg ~/.local/bin/
chmod +x ~/.local/bin/winpkg
fish_add_path ~/.local/bin
```

`fish_add_path` persists automatically — no need to edit `config.fish`. Verify:

```fish
which winpkg
```

<details>
<summary>Older fish (no fish_add_path)</summary>

Add to `~/.config/fish/config.fish`:

```fish
if not contains $HOME/.local/bin $PATH
    set -U fish_user_paths $HOME/.local/bin $fish_user_paths
end
```

Then restart your terminal or `source ~/.config/fish/config.fish`.
</details>

## Usage

winpkg always lives in `~/.local/share/winpkg` — it doesn't matter what directory you're in when you run these commands.

```fish
winpkg init          # once, ever
```

Build (install + set up a launcher) for an app:

```fish
winpkg build app testapp ~/Downloads/something-small.exe
winpkg status
winpkg run app testapp
```

`winpkg build` will sometimes stop and ask you to pick an `.exe` — only when it finds more than one candidate (e.g. Office offering `WINWORD.EXE`, `EXCEL.EXE`, ...). If there's only one, it picks it automatically.

For dependencies (VC++ runtimes, .NET, etc.), pass them after the installer:

```fish
winpkg build app office365 ~/Downloads/OfficeSetup.exe vcrun2022 dotnet48 msxml6
```

For games, use `game` instead of `app` — this uses `umu-run`/Proton instead of plain Wine:

```fish
winpkg build game cyberpunk ~/Downloads/setup.exe
```

## If things go wrong

**`winpkg init` fails** — `wine`/`winetricks` probably aren't installed:

```fish
sudo pacman -S wine winetricks
```

If pacman says "target not found," `[multilib]` isn't enabled — uncomment it in `/etc/pacman.conf`, then `sudo pacman -Sy` and retry.

**Games need `umu-launcher`** (AUR, not in the main repos):

```fish
paru -S umu-launcher
```

**An install/update broke something** — every `deps`/`install` auto-snapshots first, so roll back:

```fish
winpkg snapshots app office365          # list available snapshots
winpkg restore app office365 pre-install-20260711123456
```

**Something's just acting weird** — check its health:

```fish
winpkg doctor app office365
```

**Deleted something by mistake** — `remove` moves to trash by default, it doesn't delete outright:

```fish
winpkg trash-list
winpkg trash-restore app office365
```

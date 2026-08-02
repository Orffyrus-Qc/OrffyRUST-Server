# OrffyRUST Server Script

A single Windows batch file that installs, configures, updates, runs, watchdogs, and backs up a
[Facepunch Rust](https://rust.facepunch.com/) dedicated server — including its Oxide or Carbon
modding framework and a curated set of uMod/Dropbox-hosted plugins.

Everything the script does is driven from one file: `_OrffyRUST_v1.9.2.bat`. No external
installer, no separate config tool — you edit the `.bat` in Notepad++ and re-run it.

> This script was written for personal use on a Windows Rust server host. It is shared here for
> reference and backup. Read [SETUP.md](SETUP.md) before running it on your own machine — several
> values (RCON password, Dropbox links) are placeholders that you must fill in.

## What it does

Each run walks through these stages in order:

1. **SteamCMD bootstrap** — downloads and unpacks `steamcmd.exe` if it isn't already present.
2. **Rust dedicated server install/update** — runs
   `steamcmd +force_install_dir ./_RUST/ +login anonymous +app_update 258550 ... +quit`
   to install or update the Rust server app (`258550`) into `_rust/`. A beta branch line
   (`aux02` / `staging`) is included and can be swapped by commenting/uncommenting.
3. **Modding framework install** — installs **either** [Carbon](https://carbonmod.gg/) **or**
   [Oxide](https://umod.org/) (never both — see [SETUP.md](SETUP.md)), including first-run folder
   scaffolding (`oxide/config`, `oxide/data`, `oxide/plugins`, `oxide/data/Kits`, etc.).
4. **Plugin install/update** — one block per plugin. Each block deletes the existing copy (if any)
   and re-downloads it, either from **uMod** (`umod.org/plugins/...`) or from a **personal Dropbox
   direct-download link** for plugins/configs that are private, paid, or hand-modified. See
   [PLUGINS.md](PLUGINS.md) for the full list and how to toggle each one.
5. **Server launch** — starts `RustDedicated.exe` with hostname, map, seed, world size, RCON,
   max players, and other launch parameters (see the commented parameter reference at the top of
   the script for the full set of supported flags).
6. **Auto-restart watchdog** *(optional, off by default)* — generates a companion
   `autoRestart.bat` that polls whether the server window is still running and relaunches this
   script if it isn't.
7. **Shutdown backup** — after a clean `QUIT` from the server console, copies blueprints
   (`*.db` / `*.db-journal`), save games (`*.sav*`), Oxide permission data, server `.cfg` files, and
   `Economics.json` into a timestamped folder under `_BACKUP/`.

## Why one script

The design goal is **disaster recovery in one file**: if the host machine dies, you can rebuild
the entire server — Rust install, mod framework, every plugin, every config — by copying this
script to a fresh machine, pointing it at your own Dropbox links, and running it once. See
[SETUP.md](SETUP.md) for how the Dropbox-link mechanism works and how to point it at your own
files.

## Requirements

- Windows (batch script; uses `cd`, `md`, `xcopy`, `powershell -Command`, `TASKLIST`)
- [SteamCMD](https://developer.valvesoftware.com/wiki/SteamCMD) (auto-downloaded on first run if
  missing)
- PowerShell available on `PATH` (used for all downloads via `Invoke-WebRequest` /
  `Net.WebClient`)
- Windows firewall set to **Private networks** the first time the server binds its ports (you'll
  get a "Windows Security Alert" prompt — accept Private only)
- ~10+ GB free disk space for the Rust server install, plus space for map data and backups

## Quick start

1. Read [SETUP.md](SETUP.md) in full first — it covers renaming the script, RCON password,
   map/seed selection, Oxide vs. Carbon, and the Dropbox-link mechanism.
2. Rename the script if you want (see the header comment / [SETUP.md](SETUP.md) for the exact
   Notepad++ steps — a plain rename isn't enough because the title/watchdog logic reference the
   filename internally).
3. Edit the server parameters block (hostname, map, seed, world size, RCON password, max players).
4. Decide which plugins you want (see [PLUGINS.md](PLUGINS.md)) and toggle them accordingly.
5. Double-click the `.bat` (or run it from a terminal) and let it install everything.
6. In the Rust server console, type `server.save` then `QUIT` to shut down cleanly — this
   triggers the backup step.

## Repo layout

```
_OrffyRUST_v1.9.2.bat   The entire script — install, update, launch, watchdog, backup
SETUP.md                Configuration walkthrough: renaming, RCON, maps, Oxide/Carbon, Dropbox links
PLUGINS.md              Full plugin list, source (uMod vs. Dropbox), and enabled/disabled state
.gitignore              Excludes the server install, backups, and generated files from version control
```

Everything the script downloads or generates at runtime (`_rust/`, `_BACKUP/`, `steamcmd*`,
`autoRestart.bat`, etc.) is intentionally excluded via `.gitignore` — only the launcher and its
docs are tracked.

## Security note

- The RCON password in this repo's copy of the script is a placeholder
  (`YOUR_RCON_PASSWORD_HERE`) — put your real one in locally, and never commit it.
- Several plugin/config downloads point at personal Dropbox "direct download" links
  (`...?rlkey=...&dl=1`). Anyone with that URL can fetch the linked file, so treat those links
  like credentials: don't paste them into public issues, forks, or chat.

## Credits

Script and plugin configuration by **Orffyrus**. Plugins themselves are third-party, sourced from
[umod.org](https://umod.org/) or linked authors — see [PLUGINS.md](PLUGINS.md) for attribution
notes embedded in the script.

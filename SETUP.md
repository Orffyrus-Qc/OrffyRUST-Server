# Setup Guide

This walks through every piece of the script you're expected to configure before running it.
Line numbers refer to `_OrffyRUST_v1.9.3.bat` in this repo.

## 1. Renaming the script

The filename is also used internally (window title, watchdog process lookup), so a plain
Windows rename isn't enough. Instead:

1. Rename the `.bat` file itself first.
2. Open it in [Notepad++](https://notepad-plus-plus.org/).
3. Select the `Title` line near the top, copy the old name.
4. Open Find & Replace (`Ctrl+H`), "Replace All" every occurrence of the old name with the new
   one.

The script also contains a hard warning about batch line-continuation syntax:

> This sign `^` needs to be added at the end of each line but not the last line. If you put it at
> the end of a commented line it will break the code.

Keep that in mind if you edit the launch-parameter block.

## 2. Server launch parameters (lines ~63–72)

```bat
call RustDedicated.exe -batchmode -server.port 28015 -queryport 28016 -rcon.port 28017 -app.port 28018 -logfile^
-server.hostname "OrffyRUST_PRIVATE" ^
-server.level "Procedural Map"
-server.seed 2322396
-server.worldsize 1004
-rcon.password "YOUR_RCON_PASSWORD_HERE" ^
-server.identity "backupSystem" ^
-server.maxplayers 120 ^
-server.secure 0
```

- **`-rcon.password`** — set this to your own password before running. Don't commit a real one to
  version control.
- **`-server.identity`** — leave this alone. The auto-backup logic depends on this exact value.
- **`-server.level` / `-server.seed` / `-server.worldsize`** — for a default procedural map. For a
  custom map instead, use `-server.levelurl` pointing at a `.map` file (Dropbox share link, or
  [rustmaps.com](https://rustmaps.com/) with a free account for up to 5 uploaded custom maps).
  Local single-player testing can use `file:///C:/Rust_Maps/YourMap.map` directly.
- A full reference of optional flags (`-fps.limit`, `-decay.scale`, `-wipeDayofWeek`,
  `-bradley.respawndelayminutes`, `-hackablelockedcrate.requiredhackseconds`, etc.) is documented
  as comments directly above the launch block in the script — copy any of them into the launch
  command as needed.

## 3. Oxide vs. Carbon

The script installs **one** modding framework, never both. Two labeled sections exist
(`INSTALL CARBON` / `INSTALL OXIDE`); each is skipped by default via a `GOTO` jump.

- To use **Oxide** (default active path): leave the Carbon section jumped and let the Oxide
  section run.
- To use **Carbon** instead: comment out `GOTO :JUMP-CARBON` so the Carbon install block executes,
  and make sure the Oxide block is skipped.

Only enable one — running both against the same Rust install will conflict.

## 4. Toggling plugins on/off

Every plugin lives in its own block shaped like this:

```bat
::SomePlugin.cs
GOTO :JUMP_SomePlugin.cs
cd _rust\oxide\plugins\ && IF EXIST "SomePlugin.cs" (del /Q SomePlugin.cs && goto :SomePlugin.cs-download) ELSE (goto :SomePlugin.cs-download)
:SomePlugin.cs-download
powershell -Command Invoke-WebRequest -Uri "https://umod.org/plugins/SomePlugin.cs" -OutFile ".\SomePlugin.cs"
cd.. && cd.. && cd..
echo  SomePlugin.cs Updated
:JUMP_SomePlugin.cs
```

- `GOTO :JUMP_SomePlugin.cs` **not commented** → the jump executes → the download is **skipped**
  → plugin **disabled**.
- `::GOTO :JUMP_SomePlugin.cs` **commented out** → the jump never runs → the block **executes** →
  plugin **enabled/updated** on every run.

To enable a plugin: add `::` in front of its `GOTO :JUMP_...` line. To disable it: remove the
`::`. See [PLUGINS.md](PLUGINS.md) for the current state of every plugin block and what each one
does.

## 5. The Dropbox-link mechanism (for personal/paid/modified files)

Some plugins or their configs aren't fetched from uMod — either because they require a login,
were purchased, or were hand-modified by Orffyrus (kits, GUIShop resources, building-health
tweaks, etc.). Those blocks instead download from a **Dropbox direct-download link**.

To set this up for your own files:

1. Get a free Dropbox account (2 GB is enough) and install the desktop app, choosing "keep local
   and online" when asked.
2. Mirror the Rust folder layout inside your Dropbox folder, e.g.:
   ```
   DropboxFolder/_Rust/oxide/config/
   DropboxFolder/_Rust/oxide/data/
   DropboxFolder/_Rust/oxide/data/Kits/
   DropboxFolder/_Rust/oxide/plugins/
   DropboxFolder/_Rust/server/backupSystem/cfg/
   ```
3. Right-click a file in Dropbox → **Copy Dropbox link**.
4. The copied link ends in `...&dl=0` — change that trailing `0` to `1` to force a direct
   download instead of opening a browser preview page.
5. Paste the modified link into the matching `call :DropboxGet "URL" ".\filename"`
   line in the script, replacing `pasteYourDropboxLinkHere`.
6. Keep the URL inside those quotes. An `&` in the Dropbox link is part of the URL there and
   does not need the old `\"" & \""` escape.

If a link is still `pasteYourDropboxLinkHere`, or Dropbox no longer has the file (deleted share, or the HTML "Invalid Link" page), the script does not stop. That download is skipped, any copy already on disk is left in place, and the script continues with the next plugin. The server still launches.

Files referenced by this mechanism throughout the script:

- `oxide/plugins/*.cs` — plugins that can't auto-download normally
- `oxide/config/*.json` — matching plugin configs
- `oxide/data/**` — plugin data (Kits, MonumentAddons, GUIShop, HumanNPC, etc.)
- `oxide/data/oxide.groups.data` / `oxide.users.data` — Oxide permission groups/users
- `oxide/oxide.config.json` — controls Community/Modded server-list listing and the plugin
  watcher (auto-load newly added plugins)
- `server/backupSystem/cfg/bans.cfg` — ban list
- `server/backupSystem/cfg/users.cfg` — owner/admin SteamIDs (`ownerid <steamid>` /
  `removeowner <steamid>`)
- `server/backupSystem/cfg/serverauto.cfg` — console/F1-panel settings saved with `writecfg`
  (e.g. `antihack.flyhack_protection 0`, `hackablelockedcrate.requiredhackseconds 300`)
- `oxide/data/copypaste/` — in-game copied structures (only persist until the server restarts —
  copy anything you want to keep to your Dropbox before restarting)

Treat every Dropbox link you paste in like a credential — anyone with the URL can pull the linked
file.

## 6. Advanced: restoring permissions / console config from Dropbox on every start

Two sections are gated behind extra opt-in jumps (`useAdvancePerms`, `useAdvanceCfg`) so that,
by default, the script only **backs up** `oxide.groups.data`/`oxide.users.data` and
`serverauto.cfg`/`users.cfg`/`bans.cfg` into `_BACKUP/` rather than overwriting them from Dropbox
on every launch. Uncomment the corresponding `GOTO :useAdvance...` line only if you specifically
want your Dropbox copies to be treated as the source of truth on every restart.

## 7. Auto-restart watchdog

Off by default (`GOTO :JUMP_autoRestart` skips the generation block). If enabled, the script
writes out a companion `autoRestart.bat` that checks every 10 seconds whether the server window
is still running and relaunches this script if it closed unexpectedly. The script notes a known
issue where this can loop on some Windows 11 / admin Command Prompt configurations — it's
confirmed to work fine on Windows 10.

## 8. Shutting down safely

In the Rust server console:

```
server.save
QUIT
```

`QUIT` (not closing the window) is what saves blueprints, Economics, user auth levels, and Oxide
permissions, and triggers this script's post-shutdown backup step into `_BACKUP/`.

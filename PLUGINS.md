# Plugin Reference

Every plugin/config the script can install, grouped by the section headers used inside
`_OrffyRUST_v1.9.2.bat`. **State** reflects this snapshot of the script — see
[SETUP.md § Toggling plugins](SETUP.md#4-toggling-plugins-onoff) for how to flip a plugin on or
off yourself (comment/uncomment its `GOTO :JUMP_...` line).

Legend:
- **Enabled** — downloaded/updated on every run
- **Disabled** — block is skipped (jump not commented out)
- **Source**: *uMod* = public `umod.org/plugins/...` URL · *Dropbox* = personal direct-download
  link (some are placeholders you must fill in yourself, see [SETUP.md](SETUP.md))

A Dropbox link that does not exist is skipped. The script keeps any copy already on disk and continues.

## Server Administration

| Plugin | State | Source | Notes |
|---|---|---|---|
| `PermissionsManager.cs` | **Enabled** | Dropbox | Orffyrus-hosted copy |
| `PlayerAdministration.cs` | Disabled | uMod | |
| `Vanish.cs` | Disabled | uMod | Admin invisibility |
| `AdminRadar.cs` | **Enabled** | uMod | Admin ESP/radar |
| `AdminRadar.json` | Disabled | Dropbox | Config for `AdminRadar.cs` |
| `NoGiveNotices.cs` | Disabled | uMod | Suppresses admin `give` chat notices |
| `RaidTracker.cs` | Disabled | uMod | Raid alerts |
| `RaidTracker.json` | Disabled | Dropbox | Discord webhook settings |
| `AdminLogger.cs` | Disabled | uMod | Admin action logging |
| `AdminLogger.json` | Disabled | Dropbox *(placeholder link)* | Discord webhook settings |
| `NoGreen.cs` | Disabled | uMod | Removes green "safe zone" build markers |
| `AdminHammer.cs` | Disabled | uMod | Admin building-inspection hammer |
| `TimeOfDay.cs` | Disabled | uMod | Day/night cycle control |
| `TimeOfDay.json` | Disabled | Dropbox | Config for `TimeOfDay.cs` |
| `HeliControl.cs` | Disabled | uMod | Patrol helicopter tuning |
| `ItemRetriever.cs` | Disabled | uMod | Retrieve items dropped/lost |
| `ServerArmour.cs` | Disabled | uMod | Anti-cheat/server hardening |
| `ServerArmour.json` | Disabled | Dropbox *(placeholder link)* | Config for `ServerArmour.cs` |
| `CopyPaste.cs` | Disabled | uMod | Structure copy/paste |
| `CopyPaste.json` | Disabled | Dropbox *(placeholder link)* | Config for `CopyPaste.cs` |
| `copypasteDATA` | Disabled | Dropbox (zip of `oxide/data/copypaste/`) | Restores previously saved copy/paste structures |

## Server Plugins

| Plugin | State | Source | Notes |
|---|---|---|---|
| `Oxide.Ext.Discord.dll` | Disabled | Dropbox | Discord extension for Oxide |
| `PlaceholderAPI.cs` | Disabled | uMod | Text placeholder API for other plugins |
| `ImgurApi.cs` | Disabled | uMod | Imgur upload API for other plugins |
| `RustMapApi.cs` | Disabled | uMod | Map image API |
| `BuildingSkins.cs` | Disabled | uMod | Custom building skins |
| `SkinBox.cs` | Disabled | Dropbox | Skin selection UI |
| `ChaosExtensionDownloader.cs` | Disabled | Dropbox | Chaos Oxide extension downloader |
| `GroupLimits.cs` | Disabled | uMod | Per-group building/team limits |
| `MaxCupboardAuths.cs` | Disabled | uMod | Caps tool cupboard authorized-user count |
| `DoorLimiter.cs` | Disabled | uMod | Limits doors per base |
| `AAlertRaid.cs` | Disabled | Dropbox | Raid alert plugin — enabling it also creates `oxide/data/AAlertRaid/` (see `JUMP_AAlertRaid_md` in the script) |
| `FERMENSData.json` | Disabled | Dropbox | Data file for `AAlertRaid.cs` |
| `AntiOfflineRaid.cs` | Disabled | uMod | Offline raid protection |
| `AntiOfflineRaid.json` | Disabled | Dropbox *(placeholder link)* | Modified by Orffyrus |
| `TruePVE.cs` | Disabled | uMod | PvE/PvP damage rules engine |
| `TruePVE.json` | Disabled | Dropbox *(placeholder link)* | Config for `TruePVE.cs` |
| `RaidHours.cs` | Disabled | Dropbox *(placeholder link)* | Based on TruePVE (by nivex), modified by Orffyrus to restrict raiding to set hours; needs rebuilding after TruePVE updates. Works with `ZoneManager.cs` |
| `RaidHours.json` | Disabled | Dropbox *(placeholder link)* | Config for `RaidHours.cs` |
| `ZoneManager.cs` | Disabled | uMod | Zone definitions, used by `RaidHours.cs` |

## CustomVendingSetup and MonumentAddons

| Plugin | State | Source | Notes |
|---|---|---|---|
| `CustomVendingSetup.cs` | Disabled | uMod | Custom vending-machine contents |
| `CustomVendingSetup.json` | Disabled | Dropbox | Modified by Orffyrus |
| `MonumentFinder.cs` | Disabled | uMod | Dependency for `MonumentAddons.cs` |
| `MonumentAddons.cs` | Disabled | uMod | Add custom entities/spawns at monuments |

## BuildingHealth and OtherBuildingHealth

| Plugin | State | Source | Notes |
|---|---|---|---|
| `RaidblockBuildingHealth.cs` | Disabled | uMod | Raid-block integration for building health |
| `BuildingHealth.cs` | Disabled | uMod | Adjustable structure HP |
| `BuildingHealth.json` | Disabled | Dropbox | Modified by Orffyrus |
| `OtherBuildingHealth.cs` | Disabled | Dropbox | Modified by Orffyrus — removed double health on barrels |

## Economics and dependency

> If using `HumanNPC.cs` and the map changes (custom map swap or new procedural map), delete
> `oxide/data/HumanNPC/NpcData.json` — it stores NPC trader spawn locations tied to the old map.
>
> To add a new trader for `GUIShop.cs` + `HumanNPC.cs`: spawn the NPC in-game as admin
> (`/npc_add`, name it, `/npc_end`), copy its `userid` from
> `oxide/data/HumanNPC/NpcData.json`, paste it into the matching `"NPCId"` field in
> `oxide/config/GUIShop.json`, save, then run `o.reload GUIShop` in the F1 console.

| Plugin | State | Source | Notes |
|---|---|---|---|
| `Economics.cs` | Disabled | uMod | Player currency backend |
| `EconomicsBalanceGUI.cs` | Disabled | uMod | On-screen balance display |
| `EconomicsBalanceGUI.json` | Disabled | Dropbox | Modified by Orffyrus — display config/positioning |
| `GUIShop.cs` | Disabled | uMod | NPC/GUI trading shop |
| `GUIShop.json` | Disabled | Dropbox | Modified by Orffyrus, traders/resources config. Downloaded **only if missing**, to avoid wiping saved NPC IDs |
| `HumanNPC.cs` | Disabled | uMod | Human-like NPC framework, used for traders |
| `NpcData.json` | Disabled | Dropbox *(placeholder link)* | Modified by Orffyrus — NPC trader spawn data |
| `PathFinding.cs` | Disabled | uMod | Dependency for NPC movement |
| `Waypoints.cs` | Disabled | uMod | Dependency for NPC patrol/movement |
| `ImageLibrary.cs` | Disabled | uMod | Shared image-loading dependency for GUI plugins |

## Kits (for Economics NPCs)

| Plugin | State | Source | Notes |
|---|---|---|---|
| `Kits.cs` | Disabled | uMod | Kit-giving plugin |
| `kits_data.json` | Disabled | Dropbox | Modified by Orffyrus — kits for trader NPCs. Downloaded **only if missing** |
| `Kits.json` | Disabled | Dropbox *(placeholder link)* | Config for `Kits.cs` |
| `player_data.json` | Disabled | Dropbox *(placeholder link)* | Per-player kit cooldown data |

## Core config

| File | State | Source | Notes |
|---|---|---|---|
| `oxide.config.json` | Disabled | Dropbox | Controls Community/Modded server-list listing and the plugin-watcher (auto-load new plugins) |

## Advanced / opt-in only

These are gated behind an extra `useAdvancePerms` / `useAdvanceCfg` jump and are off even when
their individual blocks below them are toggled — see
[SETUP.md § 6](SETUP.md#6-advanced-restoring-permissions--console-config-from-dropbox-on-every-start):

| File | Purpose |
|---|---|
| `oxide.groups.data` | Oxide permission groups |
| `oxide.users.data` | Oxide per-user permissions |
| `serverauto.cfg` | Console/F1-panel settings saved via `writecfg` |
| `users.cfg` | Owner/admin SteamID list |
| `bans.cfg` | Ban list |

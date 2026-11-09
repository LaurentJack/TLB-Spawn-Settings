# TLB Spawn: settings

[Version française](README.md)

This document describes every setting of the **TLB Spawn** DayZ mod: spawn selection menu, the map's vanilla spawn
points, per-spawn cooldowns, death block and base sleeping bags.

All settings live in a single file: `<server profile folder>/TLB_Spawn/TLB_Spawn_Settings.json`.
It is created on first start with the default values ([`files/TLB_Spawn_Settings.json`](files/TLB_Spawn_Settings.json)),
then rewritten on every start to add new settings. A filled-in example is in
[`files/TLB_Spawn_Settings.example.json`](files/TLB_Spawn_Settings.example.json).

**Before editing the file**

- Yes / no: `1` = yes, `0` = no.
- Durations in seconds, distances in metres.
- Positions: `[x, y, z]` (y = height). Take them from the admin menu ("Add my position") rather than typing them.
- Almost everything can be set in game in the **admin menu** (**Home** key, rebindable in
  Options > Controls > TLB Spawn). It rewrites this file when you click "Save". Only the `AdminIDs` list must be edited
  in the file.
- Edit the file while the server is stopped, or use the admin menu: a file edited while the server runs is overwritten
  the next time the menu saves.

## Installation

1. Load the mod on **both client and server**. It has no dependencies (CF not required).
2. Add the entries from [`files/types_tlbspawn.xml`](files/types_tlbspawn.xml) to your mission's `types.xml`:
   - `TLBSP_SleepingBag`: packed sleeping bag, found as loot (or sold by a trader);
   - `TLBSP_SleepingBag_Deployed`: placed bag, 45-day lifetime (refreshed by a territory flag).
3. Start the server once: the settings file is created.
4. Put your SteamID64 in `AdminIDs`, restart, then configure the rest in game.

## How it works

- **New character** (mode `0`): the menu opens and the player picks a spawn. They are invulnerable while choosing. A
  pending choice survives a disconnect. After `SelectionTimeoutSeconds`, a random free spawn is assigned.
- **Vanilla spawns**: every `<group name="…">` in the `fresh` section of the mission's `cfgplayerspawnpoints.xml`
  becomes a menu entry. A group whose positions are more than `VanillaGroupMaxSpread` apart is split into several
  entries ("North Berezino", "North Berezino 2"). An isolated position (an error in the vanilla file) is ignored.
  Positions outside any group are clustered and named after the nearest town.
- **Cooldown**: after spawning at a point, that point is unavailable to that player for `CooldownSeconds`. Cooldowns are
  stored in UTC in `TLB_Spawn/Players/<player>.json`: they survive relogs and restarts.
- **Death block**: if the player dies within `DeathBlockRadius` metres of a spawn, that spawn is unavailable to them for
  `DeathBlockSeconds`.
- **Sleeping bags**: the player places the packed bag with the hologram. The deployed bag becomes their respawn point
  ("Sleeping Bag (Elektrozavodsk)"). It is packed back with the "Pack sleeping bag" action. The "Random" button never
  picks a bag.

## General settings

| Setting | Default | Description |
|---|---|---|
| `Version` | `1` | File format version, managed by the mod: do not change |
| `Enabled` | `1` | 1 = mod active; 0 = the mod does nothing (vanilla spawning, bags refused) |
| `SpawnMode` | `0` | `0` = forced menu for every new character; `1` = vanilla spawning, no menu |
| `SelectionTimeoutSeconds` | `300` | Time allowed to choose before a random spawn is assigned (0 = no limit) |
| `InvulnerableWhileSelecting` | `1` | 1 = the player takes no damage until they have chosen |
| `AllowRandomButton` | `1` | 1 = shows the "Random" button (random free vanilla spawn) |
| `MinDistanceToPlayers` | `80` | For a spawn with several positions, avoids those within X m of another player (0 = off) |
| `AdminIDs` | `[]` | Admins' SteamID64 (e.g. `"76561198000000000"`). The player's hashed ID, shown in the server log on every spawn, is also accepted |

## Vanilla spawns

| Setting | Default | Description |
|---|---|---|
| `UseVanillaSpawnPoints` | `1` | 1 = the groups of the mission's `cfgplayerspawnpoints.xml` appear in the menu |
| `VanillaSection` | `"fresh"` | Section of the vanilla file to read: `"fresh"`, `"hop"` or `"travel"` |
| `VanillaGroupMaxSpread` | `1500` | Distance (m) beyond which a group is split into several spawns |
| `VanillaDefaults.CooldownSeconds` | `600` | Default cooldown of vanilla spawns |
| `VanillaDefaults.DeathBlockRadius` | `500` | Default death block radius |
| `VanillaDefaults.DeathBlockSeconds` | `1800` | Default death block duration (0 = never blocked) |
| `VanillaOverrides` | `[]` | Settings for a specific vanilla group (see below). The admin menu creates them for you |
| `VanillaOverrides[].GroupName` | | Group name in the vanilla file (e.g. `"WestCherno"`), or a split name (`"NorthBerezino_2"`) |
| `VanillaOverrides[].DisplayName` | `""` | Name shown in the menu (`""` = group name, e.g. "West Cherno") |
| `VanillaOverrides[].Enabled` | `1` | 0 = this spawn no longer appears in the menu |
| `VanillaOverrides[].CooldownSeconds` | | Cooldown of this spawn |
| `VanillaOverrides[].DeathBlockRadius` | | Death block radius for this spawn |
| `VanillaOverrides[].DeathBlockSeconds` | | Death block duration for this spawn |

Chernarus groups (`fresh` section): `WestCherno`, `EastCherno`, `WestElektro`, `EastElektro`, `Kamyshovo`,
`Solnechny`, `Nizhnee`, `SouthBerezino`, `Svetlojarsk`, `Orlovets`, `NorthBerezino`, `NorthBerezino_2`.

## Custom spawns

Spawns created by an admin, listed first in the menu with a star. Easiest way: admin menu > Spawns >
"New spawn here".

| Setting | Default | Description |
|---|---|---|
| `CustomSpawns[].Name` | | Displayed name (also used as the cooldown ID: renaming it resets cooldowns) |
| `CustomSpawns[].Enabled` | `1` | 0 = spawn hidden |
| `CustomSpawns[].Positions` | | One or more `[x, y, z]` positions. One is picked at random, avoiding other players |
| `CustomSpawns[].ScatterRadius` | `0` | 0 = exact position, height kept (works on a building's upper floor); otherwise random scatter in metres, placed on the ground |
| `CustomSpawns[].CooldownSeconds` | | Cooldown of this spawn |
| `CustomSpawns[].DeathBlockRadius` | | Death block radius |
| `CustomSpawns[].DeathBlockSeconds` | | Death block duration |

An exact spawn placed below the ground is automatically raised to terrain level.

## Spawn zone

| Setting | Default | Description |
|---|---|---|
| `SpawnZone.OverrideDefaultSpawn` | `0` | 1 = new characters appear in the zone (they wait there while choosing) |
| `SpawnZone.Radius` | `10` | Radius (m) around the picked position |
| `SpawnZone.Positions` | `[]` | One or more zone positions (admin menu > General > "Add my position to zone") |

## Sleeping bags

| Setting | Default | Description |
|---|---|---|
| `SleepingBags.Enabled` | `1` | 0 = bags can't be placed and existing bags no longer appear in the menu |
| `SleepingBags.DisplayName` | `"Sleeping Bag"` | Name shown in the menu, followed by the nearest town |
| `SleepingBags.MaxPerPlayer` | `1` | Placed bags per player (0 = unlimited). Beyond that, placing is refused with a message |
| `SleepingBags.CooldownSeconds` | `1800` | Cooldown between two spawns on the same bag |
| `SleepingBags.MaxUses` | `0` | Spawns per bag (0 = unlimited). The count follows the bag when it is packed |
| `SleepingBags.DestroyWhenUsedUp` | `1` | 1 = the bag disappears once `MaxUses` is reached |
| `SleepingBags.DeathBlockRadius` | `150` | Dying within X m of your bag blocks it... |
| `SleepingBags.DeathBlockSeconds` | `900` | ...for Y seconds (0 = never blocked) |
| `SleepingBags.OnlyOwnerCanPack` | `0` | 0 = anyone can pack a bag (raiders can remove someone's respawn point); 1 = owner only |
| `SleepingBags.MinDistanceToSpawnZone` | `200` | Forbids placing a bag within X m of the spawn zone (0 = no limit) |

## Admin menu

**Home** key, reserved for players listed in `AdminIDs`. Three tabs:

- **Spawns**: custom spawns (star) and vanilla spawns. For a custom spawn: name, enabled, cooldown, death block,
  scatter, "Add my position", "Remove last position", "Teleport", "Delete". For a vanilla spawn: display name, enabled,
  cooldown, death block. "New spawn here" creates a spawn at your position.
- **Sleeping bags**: every `SleepingBags` setting.
- **General**: mod enabled, mode, selection timeout, invulnerability, random button, distance to players, vanilla
  spawns, spawn zone.

"Save" applies the changes immediately and rewrites `TLB_Spawn_Settings.json`.

## Player files

`TLB_Spawn/Players/<id>.json`: pending choice, last death (position and time) and running cooldowns. Managed by the
mod. Deleting a file resets that player's cooldowns.

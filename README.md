# NadeLauncher

> A CS2 server plugin for practicing grenade lineups — save, browse, and share.
> Made by [QQundalf](https://steamcommunity.com/id/der-sanit-ter/)

**NadeLauncher** is a [CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp) plugin that lets players save grenade positions with full metadata (throw type, strength, movement path), browse lineups per map, and share lineups with friends. Works entirely offline — no account or external service required.

![Press E on a marker to teleport, select the fixpoint and throw](docs/using-flash.gif)

---

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Updating](#updating)
- [No Server? Easy Setup](#no-server-easy-setup)
- [Configuration](#configuration)
  - [NadeLauncher.json](#nadelauncherjson)
  - [Shared Server Usage](#shared-server-usage)
- [Commands](#commands)
  - [Lineup Management](#lineup-management)
  - [Navigation](#navigation)
  - [Utilities](#utilities)
  - [Owner-Only Commands](#owner-only-commands)
- [Throw Types](#throw-types)
- [Sharing Lineups](#sharing-lineups)
- [Playing With Friends](#playing-with-friends)
- [Troubleshooting](#troubleshooting)
- [Credits](#credits--acknowledgements)

---

## Features

- **Save lineups** with automatic detection of throw type, strength, movement path, and grenade landing position
- **11 throw types** detected automatically: normal, jumpthrow, walkthrow, runjumpthrow, duckthrow, and more
- **3 throw strengths**: full (left click), medium (both clicks), short (right click)
- **Browse lineups** per map with in-game menus, filterable by grenade type
- **Stack multiple lineups** on the same position — throw them one after another
- **Ghost replay** — press E on a lineup marker, then E again to start the replay. Automatically replays the movement path, crosshair position and grenade throw
- **Import / export lineups** as ZIP files to share with friends
- **Open server mode** — leave `OwnerSteamIds` empty to give all players full access

![Two smokes stacked on the same position — throw both back to back](docs/two-stacked-smokes.gif)

![Ghost replay — press E on a lineup marker, press E again to start the replay. The plugin automatically replays the crosshair position, movement path and grenade throw.](docs/ghost-replay.gif)

---

## Requirements

| Requirement | Version |
|---|---|
| [CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp/releases) | v1.0.365 or newer |
| [Metamod:Source](https://www.sourcemm.net/downloads.php?branch=master) | 2.0+ |
| CS2 Dedicated Server | Latest |

---

## Installation

1. Download `NadeLauncher.dll` from the [latest release](../../releases/latest)
2. Place it in your server's plugin directory:
   ```
   csgo/addons/counterstrikesharp/plugins/NadeLauncher/NadeLauncher.dll
   ```
3. Restart the server
4. The plugin generates its config automatically on first load at:
   ```
   csgo/addons/counterstrikesharp/configs/plugins/NadeLauncher/NadeLauncher.json
   ```

That's it. No additional dependencies or setup required.

---

## Updating

1. Stop the server
2. Download the new `NadeLauncher.dll` from the [latest release](../../releases/latest)
3. Replace the existing file in `csgo/addons/counterstrikesharp/plugins/NadeLauncher/`
4. Start the server

Your lineups and configuration are stored separately and are **never affected by plugin updates**.

---

## No Server? Easy Setup

Don't have a CS2 dedicated server? Download **NadeLauncher-Server.zip** — a self-contained package that installs and runs everything automatically on your Windows PC with a single double-click.

| Package | Description |
|---|---|
| [**NadeLauncher-Server.zip**](NadeLauncher-Server.zip) | One-click installer: sets up CS2 server + Metamod + CounterStrikeSharp + plugin automatically |

**What's inside:**

```
NadeLauncher-Server.zip
├── NadeLauncher-Server.bat   ← double-click this to install and start
├── config.ini                ← change install paths and player slots here
├── setup-network.bat         ← open firewall + see your IPs for friend hosting
├── export-lineups.bat        ← export your lineups as a ZIP to share
├── import-lineups.bat        ← import lineups received from a friend
├── README.txt                ← offline instructions
└── plugin\
    └── NadeLauncher.dll      ← the plugin (installed automatically)
```

**On first run, the bat automatically:**
1. Downloads and installs **SteamCMD** (~50 MB)
2. Downloads and installs the **CS2 Dedicated Server** (~60 GB, 30–90 min)
3. Installs **Metamod:Source** and **CounterStrikeSharp**
4. Writes all server configs
5. Installs the plugin and starts the server

**Every subsequent run:** checks for CS2 updates and starts in ~30 seconds.

**Requirements for the ZIP:**
- Windows 10 or Windows 11 (64-bit)
- ~70 GB free disk space
- Internet connection for first-time setup
- CS2 installed on Steam (to connect as a client)

**Connecting:**
Open CS2, press `~` to open the console and type:
```
connect localhost:27015
```

### Updating the plugin (ZIP users)

1. Stop the server (close the server console window)
2. Download the new `NadeLauncher-Server.zip` from the [latest release](../../releases/latest)
3. Extract and copy the `plugin\` folder into your existing NadeLauncher folder, replacing the old files
4. Start the server again

---

## Configuration

### NadeLauncher.json

Auto-generated on first load at:
```
csgo/addons/counterstrikesharp/configs/plugins/NadeLauncher/NadeLauncher.json
```

| Field | Type | Default | Description |
|---|---|---|---|
| `ChatPrefix` | string | `[NadeLauncher]` | Prefix shown before all plugin chat messages |
| `MenuPageSize` | int | `5` | Number of lineups shown per menu page |
| `OwnerSteamIds` | string[] | `[]` | SteamID64s with owner access. **Empty = everyone is owner** |
| `AutoActivate` | bool | `true` | If `true`, the plugin activates automatically when the server starts. Set to `false` on shared servers — owners use `!nadelauncher on/off` to activate it manually. |

**Finding your SteamID64:** go to [steamid.io](https://steamid.io) and look up your profile. Use the `steamID64` value (e.g. `76561197978877739`).

Example with owner restriction:
```json
{
  "ConfigVersion": 1,
  "ChatPrefix": " \u0004[NadeLauncher]\u0001 ",
  "MenuPageSize": 5,
  "OwnerSteamIds": ["76561197978877739", "76561198012345678"],
  "AutoActivate": true
}
```

---

### Shared Server Usage

Running NadeLauncher on a server that also hosts competitive or other game modes? Set `AutoActivate` to `false` in `NadeLauncher.json`:

```json
{
  "AutoActivate": false
}
```

With `AutoActivate: false`:
- The plugin loads but applies **no settings changes** (no `sv_cheats`, no infinite ammo, no respawn, etc.)
- Players joining get no practice welcome message
- Owners joining see a reminder: *"NadeLauncher is inactive. Type !nadelauncher on to activate."*
- Your normal server settings and other plugins are completely unaffected

When you're ready to practice, an owner types `!nadelauncher on` and all practice settings kick in. When done, `!nadelauncher off` reverts everything back to defaults.

---

## Commands

All commands work with both `!` and `.` prefix (e.g. `!save` or `.save`).

### Lineup Management

| Command | Alias | Access | Description |
|---|---|---|---|
| `!save [name]` | `!s` | Owner | Save your current position and view angles as a lineup. After running the command, throw the grenade — the plugin records the full throw automatically. Optionally provide a name. |
| `!savelast [name]` | `!sl` | Owner | Save the last grenade you threw as a lineup. Useful if you forgot to run `!save` beforehand. Captures everything from the most recent throw. |
| `!desc <text>` | — | Owner | Add a description to the last saved lineup. |
| `!lineups` | — | All | Open the lineup browser menu for the current map. Navigate pages with the in-game menu. |
| `!filter [type]` | `!f` | All | Filter the lineup menu by grenade type. Valid values: `smoke`, `flash`, `molotov`, `he`, `all`. |

---

### Navigation

| Command | Alias | Access | Description |
|---|---|---|---|
| `!goto <id>` | — | All | Teleport to a lineup by its ID. The ID is shown in the lineup browser. |
| `!pos` | — | All | Print your current position and view angles to chat. Useful for manual reference. |
| `!back` | — | All | Teleport back to the position where you last threw a grenade. |
| `!rethrow` | `!r` | All | Re-teleport to the last lineup you visited. Has a 1-second cooldown. |

---

### Utilities

| Command | Alias | Access | Description |
|---|---|---|---|
| `!clear` | `!c` | All | Remove all active smokes, molotovs, and decoys from the map instantly. |
| `!noflash` | — | All | Toggle flashbang immunity. When enabled, flashbangs will not blind you. |
| `!ff [seconds]` | — | All | Fast-forward time by the given number of seconds (1–120, default 6). Runs at 10× speed. Useful for waiting out smokes. |
| `!markers` | — | All | Refresh lineup position markers on the map. |
| `!show` | — | Owner | Show all lineup markers on the map. |
| `!hide` | — | Owner | Hide all lineup markers on the map. |

---

### Owner-Only Commands

These commands require your SteamID to be listed in `OwnerSteamIds`, or the server to be in open mode (empty list).

| Command | Alias | Description |
|---|---|---|
| `!map <name>` | — | Change the current map. The `de_` prefix is added automatically if omitted (e.g. `!map mirage` → `de_mirage`). |
| `!maps` | — | Open a map selection menu. Navigate with the mouse. |

![Map selection menu](docs/map-menu.gif)
| `!kick <name>` | — | Kick a player by name. |
| `!save` / `!savelast` | `!s` / `!sl` | Save new lineups (restricted to owners when `OwnerSteamIds` is not empty). |
| `!nadelauncher [on\|off]` | — | Activate or deactivate the plugin on the running server. Without arguments, shows the current state. Useful on shared servers with `AutoActivate: false`. |

---

## Throw Types

NadeLauncher automatically detects your throw technique based on your movement at the time of throwing.

| Throw Type | How to Perform |
|---|---|
| `normal` | Stand still, throw |
| `duckthrow` | Stand still, crouch, throw |
| `stepthrow` | Take a small step (< 50 units), throw |
| `walkthrow` | Hold walk (+speed), move, throw |
| `runthrow` | Run (no walk key), throw |
| `jumpthrow` | Jump, release grenade while in the air |
| `wjumpthrow` | Jump from a standing position (no run-up), throw |
| `duckjumpthrow` | Jump + crouch, throw |
| `stepjumpthrow` | Take a small step, jump, throw |
| `walkjumpthrow` | Hold walk, jump, throw |
| `runjumpthrow` | Run, jump, throw |

**Throw strength** is detected from which mouse buttons are held at release:

| Strength | Input |
|---|---|
| `full` | Left click only |
| `medium` | Left + right click simultaneously |
| `short` | Right click only |

---

## Sharing Lineups

### Export (send lineups to a friend)

> **ZIP users:** use the included `export-lineups.bat`.
> **Manual server users:** zip up the `data/lineups/` folder inside the plugin directory.

1. Double-click **`export-lineups.bat`**
2. A file named `nadelauncher-lineups-YYYY-MM-DD.zip` is created in the same folder
3. Send that ZIP to your friend

### Import (receive lineups from a friend)

> **ZIP users:** use the included `import-lineups.bat`.
> **Manual server users:** merge the received `lineups/` folder into your plugin's `data/lineups/` directory (don't overwrite — merge by adding new files).

**Option A — Drag and drop:**
Drag the received ZIP file directly onto **`import-lineups.bat`**

**Option B — Manual:**
1. Double-click `import-lineups.bat`
2. Paste the full path to the ZIP file and press Enter

After import the script reports how many lineups were added and how many were skipped (duplicates). Restart the server to load the new lineups in-game.

> **Note:** Imported lineups work for all players regardless of who created them. The creator SteamID is stored as metadata but is not used for access control.

---

## Playing With Friends

> This section applies to the **NadeLauncher-Server.zip** easy setup. If you're running an existing server, use your normal hosting setup.

To let friends join your server there are three options:

### Option 1 — Same Network (LAN)

If everyone is on the same Wi-Fi or local network:

1. Run **`setup-network.bat`** once to open the Windows Firewall
2. Your local IP is shown when the server starts
3. Friends connect with:
   ```
   connect 192.168.x.x:27015
   ```

### Option 2 — Internet (Port Forwarding)

1. Log in to your router (usually `http://192.168.1.1`)
2. Forward port **27015** (UDP + TCP) to your PC's local IP
3. Run `setup-network.bat` to see your public IP
4. Friends connect with:
   ```
   connect YOUR.PUBLIC.IP:27015
   ```
> Your public IP may change over time. Some ISPs do not support port forwarding.

### Option 3 — Tailscale (Recommended, No Router Changes)

[Tailscale](https://tailscale.com) is a free VPN tool that creates a private network between devices without any router configuration.

1. You and your friends all install Tailscale and join the same network
2. Run `setup-network.bat` to see your Tailscale IP (`100.x.x.x`)
3. Friends connect with:
   ```
   connect 100.x.x.x:27015
   ```

Run **`setup-network.bat`** to automate the firewall setup and display all available IPs.

---

## Troubleshooting

**Plugin not loading / `!lineups` doesn't work**
- Confirm CounterStrikeSharp is installed and loaded (server console shows CSS version on startup)
- Confirm the DLL is in `csgo/addons/counterstrikesharp/plugins/NadeLauncher/NadeLauncher.dll`
- Check the server console for any NadeLauncher error messages on startup

**"CS2 install failed" (ZIP users)**
- Make sure you have ~70 GB free on the target drive
- Check your internet connection and run the bat again — it resumes where it left off

**`connect localhost:27015` doesn't work (ZIP users)**
- Wait ~30 seconds after the server window opens for it to fully load
- Make sure Windows Firewall allows the server (click "Allow" if prompted)

**Server window closes immediately (ZIP users)**
- Right-click `NadeLauncher-Server.bat` → Run as administrator

**Lineups I imported don't show up**
- Restart the server after importing — lineups are loaded at startup
- Make sure the import script reported "X lineups added" and not errors

---

## Credits & Acknowledgements

NadeLauncher is built on top of the following open-source projects. Without their work this plugin would not exist.

- **[CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp)** by [roflmuffin](https://github.com/roflmuffin) — the C# plugin framework for CS2 that makes this possible. Huge thanks for the continued development and maintenance.
- **[Metamod:Source](https://www.sourcemm.net)** by AlliedModders — the engine-level plugin loader that CounterStrikeSharp runs on top of.

---

## License

This software is provided as-is for personal use. Source code is not publicly available.

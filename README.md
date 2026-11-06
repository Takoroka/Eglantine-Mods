# Eglantine community mods

The community mod list for [Eglantine](https://github.com/Djihads80/Eglantine). In Eglantine, open
**Tools > Mod List** and click **Refresh list**:
- **Listing:** it shows the mods here for the games in your game list.
- **Installing:** **Install** copies a mod into `load/<title id>/<mod name>/`.
- **Managing:** the game's Add-Ons tab enables and disables installed mods like any other mod.

Nothing is downloaded until you click **Refresh list**.

## Mods

| Mod | Game | Game version | Credit |
|---|---|---|---|
| Mario Kart World - 30 FPS | Mario Kart World (0400C3F00006E000) | 1.8.0 (main build ID D00E4F399E411D0613AA8D26B1C02FEF) | Franz (FZout), Eglantine PR #403 |
| Mario Kart World - Dynamic FPS | Mario Kart World (0400C3F00006E000) | 1.8.0 (main build ID D00E4F399E411D0613AA8D26B1C02FEF) | Franz (FZout), Eglantine PR #403; based on PokeDinner 2.5.0 |

- **Alternatives:** the two Mario Kart World mods are alternatives, so enable only one.
- **Game version:** their patches only apply to 1.8.0. Another version needs a new patch, and boots
  unpatched until then.

## Layout

```
mods.json                                       the index Eglantine reads
mods/<title id>/<mod name>/exefs/<build id>.ips one folder per mod, as load/<title id>/<mod name>/
mods/<title id>/<mod name>/romfs/...
mods/<title id>/<mod name>/cheats/...
```

A mod folder holds exactly what would go in `load/<title id>/<mod name>/`. Its last part is the name
the Add-Ons tab shows. It can hold what Eglantine's mod loader supports:
- **exefs patches:** IPS or IPS32 named by the module's build ID, or IPSwitch `.pchtxt`.
- **exefs file replacements.**
- **`romfs/` files** (LayeredFS).
- **Cheats.**

### exefs patches

- **Matching a version:** an IPS patch applies only to the module whose build ID is its file name:
  `exefs/<build id>.ips`.
  - Eglantine logs a module's build ID when it loads, as `Patching NSO for name=main, build_id=...`.
  - The name may leave off the build ID's trailing zeros.
- **Offsets:** offsets are into the decompressed module with its 0x100-byte NSO header in front, so a
  code offset is `0x100 + the offset in .text`. Offsets past 16 MB need IPS32.

## Submitting a mod

Open a pull request that adds:

1. **The mod's folder** under `mods/<title id>/<mod name>/`.
2. **An entry in `mods.json`:**

```json
{
  "id": "short-unique-id",
  "name": "Game - What it does",
  "title_ids": ["0400C3F00006E000"],
  "version": "1",
  "author": "You",
  "category": "Performance",
  "description": "What it changes, which game version (and build ID) it's for, and what it conflicts with.",
  "path": "mods/0400C3F00006E000/Game - What it does"
}
```

- **`id`:** never changes. Eglantine uses it to recognize the installed mod.
- **`version`:** raise it with every change. Installed copies then show "Update available".
- **`path`:** the mod's folder. Its last part becomes the installed folder name.
- **Game version:** say which game version a mod is for. A patch for a new game version can go in
  the same folder as another `<build id>.ips`, since each applies only to its own build.
- **Credit:** credit the original author in `author` or `description`.

Please only submit content you made or have permission to share. Never submit game files,
keys or firmware.

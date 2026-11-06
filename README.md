# Eglantine graphic packs

Game patches for [Eglantine](https://github.com/Djihads80/Eglantine), used through its Graphic Packs
window (**Tools > Graphic Packs**, or **Graphic Packs** in a game's Add-Ons tab).

## Getting them

- **From Eglantine:** click **Download / update packs** in the Graphic Packs window. This
  repository is the default pack repository there. Eglantine downloads it and installs each pack
  folder into `graphic_packs/` in its user folder.
  - An update replaces the packs that came from here.
  - A pack you made yourself is never overwritten, even if it has the same folder name as a pack
    here.
- **By hand:** copy pack folders into `graphic_packs/`.

Packs are off until you enable them for a game. They take effect when the game boots.

## Packs

| Pack | Game | Presets | Credit |
|---|---|---|---|
| `MarioKartWorld_FPS` | Mario Kart World (0400C3F00006E000) | 60 FPS (default), 30 FPS, Dynamic (experimental) | Franz (FZout), Eglantine PR #403; dynamic mode based on PokeDinner 2.5.0 |

## Making a pack

Any folder with a `rules.txt` is a pack:

```
MyGame_Something/
  rules.txt
  presetA/patch.pattern       patch files, in the folders the presets name
  presetB/<build id>.ips
```

### rules.txt

```ini
[Definition]
# Comma-separated program IDs (required).
titleIds = 0400C3F00006E000
name = FPS
# Where the pack shows in the list's tree.
path = "Mario Kart World/FPS"
description = One line.\nAnother line.
version = 1
author = You

# One [Preset] section per choice in the preset list.
[Preset]
name = Off
default = 1

[Preset]
name = On
# Folders, relative to the pack folder.
patches = on
```

Comments go on their own lines (`#` or `;`). Text after a value is part of the value.

Without `[Preset]` sections, the pack applies the patch files in the folders listed in
`[Definition] patches`, or in the pack folder itself.

### Patch files

| File | What it is |
|---|---|
| `<build id>.ips` | IPS or IPS32 for the module with that build ID. Offsets include the NSO's 0x100-byte header, as for exefs mods. |
| `*.pchtxt` | IPSwitch. Its `@nsobid-` line picks the module. |
| `*.pattern` | Pattern patches. They find the code by its instructions, so they keep working across game versions. |

### Pattern patches

```ini
[Patch name]
module = main
pattern = 6B01013F 54000001/FF00001F B9421268 35000008/FF00001F
near = 4..9 52A84E08
count = 1
write = 2 52800048
find = 3..33 F9409660/FFFFFFE0
write = 0 F9407660/FFFFFFE0
```

- A word is a 32-bit instruction in hex. `value/mask` compares only the masked bits, and `*` matches
  any word.
- **`pattern`:** consecutive words. Each place in the module's code where they all match is a site,
  starting at the pattern's first word.
- **`near = a..b word`:** a word in `site+a .. site+b` must match (offsets in words).
- **`count`:** how many sites there must be (default 1). Otherwise the patch is skipped.
- Then, in order, starting at the site:
  - **`find = a..b word`:** exactly one word in that window must match; later steps work from it.
  - **`write = offset word`:** writes at that offset. With a mask, only the masked bits change.

If any step fails, that patch writes nothing, the game boots unpatched, and Eglantine's log
(`Loader`) says why. The full format is in Eglantine's `docs/GraphicPacks.md`.

## Contributing

One folder per pack, and credit the author in `rules.txt`. Pattern patches are preferred over IPS,
so a pack doesn't break with every game update.

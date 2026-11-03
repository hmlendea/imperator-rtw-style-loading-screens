# Frequently Asked Questions

## General

### What does this mod do?
Replaces all 10 vanilla loading screens in Imperator: Rome with artwork in the style of Rome: Total War (2004).

### Is this a code mod?
No. It contains only DDS image files and a mod descriptor. No scripts, no DLLs, no configuration.

### Which game versions are supported?
Imperator: Rome 2.0.x (declared via `supported_version="2.0.*"` in descriptor).

### Will it work with game version 3.0?
Not without updating `descriptor.mod` `supported_version` and testing. Major version changes may break compatibility.

## Installation

### How do I install from Steam Workshop?
1. Visit [Workshop page](https://steamcommunity.com/sharedfiles/filedetails/?id=3513150774)
2. Click "Subscribe"
3. Launch Imperator: Rome — mod auto-downloads and enables

### How do I install manually?
1. Download latest release ZIP from [GitHub Releases](https://github.com/hmlendea/imperator-rtw-style-loading-screens/releases) or [Nexus Mods](https://nexusmods.com/imperatorrome/mods/25)
2. Extract `rtw-style-loading-screens/` folder to your game's `mod/` directory
3. Enable in launcher

### Where is the mod directory?
- **Steam (Windows)**: `C:\Program Files (x86)\Steam\steamapps\common\Imperator Rome\mod\`
- **Steam (Linux)**: `~/.steam/steam/steamapps/common/Imperator Rome/mod/`
- **GOG**: `~/GOG Games/Imperator Rome/mod/`

### Do I need to delete old versions?
Yes, when manually updating. Workshop handles this automatically.

## Usage

### Can I choose which loading screens appear?
No. The game randomly selects from the 10 screens each load. No configuration exists.

### Can I add more loading screens?
No. The game only loads `load_0.dds` through `load_9.dds`. Additional files are ignored.

### Do the loading screens affect gameplay?
No. Purely visual. No impact on mechanics, performance, or saves.

### Will this work with other mods?
Yes, unless another mod also replaces loading screens (last-loaded wins). Compatible with gameplay, map, UI mods.

## Technical

### What format are the images?
DDS (DirectDraw Surface) with BC3/DXT5 compression, 3840×2160 (4K).

### Why DDS and not PNG/JPG?
Imperator: Rome's texture loader only accepts DDS for loading screens.

### Why 4K resolution?
Project specification. Game downscales for lower resolutions. Crisp on 4K displays.

### How are the assets created?
Source artwork exported from image editor (GIMP/paint.net with DDS plugin) at 4K as DDS BC3/DXT5.

### Is there a performance impact?
Negligible. Same file count and format as vanilla. Slightly larger files (4K vs vanilla) — marginally longer GPU upload.

## Development

### How do I contribute?
See [CONTRIBUTING.md](../CONTRIBUTING.md). Requires DDS-capable image editor.

### How do I create a loading screen?
1. Create artwork at 3840×2160 in your editor
2. Export as DDS (BC3/DXT5 compression)
3. Name `load_N.dds` (N = 0-9, replacing existing)
4. Test in-game
5. Submit PR

### How are releases made?
Maintainer tags commit: `git tag v1.0.1 && git push --tags`
GitHub Actions packages ZIP and publishes to Steam Workshop.
Maintainer manually uploads to Nexus Mods.

### Can I use these assets in my mod?
Yes, under GPL v3.0. See [LICENSE](../LICENSE). Derivative works must be GPL v3.0.

## Distribution

### Why three distribution channels?
- **Steam Workshop**: Auto-update for Steam players (primary)
- **Nexus Mods**: Alternative for Nexus users
- **GitHub Releases**: Source archive, manual install, non-Steam platforms

### Why is Workshop item ID important?
Immutable identifier. Changing it would orphan all 3513150774 subscribers. Never change.

### Does Nexus Mods auto-update?
No. Manual upload required per release.

## Troubleshooting

### Mod not showing in launcher
Check [Troubleshooting](./troubleshooting.md#mod-not-appearing-in-launcher).

### Vanilla screens still appearing
Check [Troubleshooting](./troubleshooting.md#loading-screens-not-replaced-vanilla-shows).

### Purple/magenta textures
Check [Troubleshooting](./troubleshooting.md#purple--missing-texture-magenta-checkerboard).

### Workshop not updating
Check [Troubleshooting](./troubleshooting.md#steam-workshop-not-updating).

## Licensing

### What license?
GNU GPL v3.0. See [LICENSE](../LICENSE).

### Can I use commercially?
GPL v3.0 permits commercial use but requires source distribution and same license on derivatives.

### Do I need to credit?
GPL requires preserving license notices. Attribution appreciated but not legally required beyond license terms.
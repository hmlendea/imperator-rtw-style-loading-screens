# Troubleshooting

## Common Issues

### Mod Not Appearing in Launcher

**Symptoms**: Mod not listed in Imperator: Rome launcher mod panel.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Mod directory not in game mod folder | Place `rtw-style-loading-screens/` in `<game>/mod/` |
| `descriptor.mod` missing or malformed | Verify file exists at `rtw-style-loading-screens/descriptor.mod` with valid Clausewitz syntax |
| `supported_version` mismatch | Check game version (2.0.x) matches `supported_version="2.0.*"` |
| Mod disabled in launcher | Enable checkbox in launcher mod list |
| Steam Workshop sync pending | Restart Steam; wait for download |

**Verification**:
```bash
# Check mod directory structure
ls -la <game>/mod/rtw-style-loading-screens/
# Should show: descriptor.mod, gfx/, thumbnail.png
```

### Mod Marked "Incompatible" in Launcher

**Symptoms**: Mod appears but shows red "Incompatible" badge.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Game version updated to 3.0+ | Update `descriptor.mod` `supported_version` to `3.0.*` (requires testing) |
| `descriptor.mod` version format wrong | Ensure `version="vX.Y.Z"` format |
| Corrupt `descriptor.mod` | Re-create from known good template |

### Loading Screens Not Replaced (Vanilla Shows)

**Symptoms**: Vanilla loading screens appear instead of RTW-style.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Missing DDS files | Verify all 10 files exist: `load_0.dds` … `load_9.dds` |
| Wrong filenames | Filenames must be exactly `load_N.dds` (lowercase, underscore) |
| Another mod overriding | Disable other loading screen mods; check load order |
| Mod not mounted | Check launcher shows mod as enabled |
| Corrupt DDS files | Re-export assets; verify in image editor |

**Verification**:
```bash
# Count loading screen files
ls -1 <game>/mod/rtw-style-loading-screens/gfx/loadingscreens/load_*.dds | wc -l
# Must output: 10
```

### Purple / Missing Texture (Magenta Checkerboard)

**Symptoms**: Loading screen shows bright magenta/purple pattern.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Corrupt DDS file | Re-export from source; verify opens in GIMP/paint.net |
| Unsupported DDS format | Use BC3/DXT5 compression; no exotic formats |
| Wrong DDS header | Re-save with DDS plugin; avoid manual hex edits |
| GPU driver issue | Update graphics drivers; rare |

### Stretched / Squished / Wrong Aspect Ratio

**Symptoms**: Loading screen distorted, not filling screen correctly.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Wrong resolution | Export at exactly 3840×2160 (4K, 16:9) |
| Non-square pixels | Ensure square pixel aspect ratio in export |
| Monitor aspect mismatch | Game handles scaling; verify source is 16:9 |

### Steam Workshop Not Updating

**Symptoms**: Subscribed mod shows old version; no update downloaded.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Git tag not pushed | `git push --tags` required to trigger workflow |
| GitHub Actions workflow failed | Check Actions tab; re-run or fix error |
| Steam client not checking | Restart Steam; verify download region |
| Workshop item ID changed | **Never change item ID** — would orphan all subscribers |

**Debug**:
1. Check GitHub Actions: `https://github.com/hmlendea/imperator-rtw-style-loading-screens/actions`
2. Verify tag exists: `git tag -l "v*"`
3. Check Workshop page: `https://steamcommunity.com/sharedfiles/filedetails/?id=3513150774`

### Nexus Mods Upload Issues

**Symptoms**: Manual upload fails or version not visible.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Wrong file format | Upload ZIP of `rtw-style-loading-screens/` directory |
| Version metadata mismatch | Update version field on Nexus edit page |
| File size limit | Nexus has limits; DDS files ~50-100 MB total — within limits |
| Category mismatch | Ensure "Imperator: Rome" category selected |

### Manual Installation Not Working

**Symptoms**: Extracted ZIP but mod doesn't load.

**Causes & Solutions**:

| Cause | Solution |
|-------|----------|
| Extracted to wrong folder | Must be `<game>/mod/rtw-style-loading-screens/` (not nested) |
| Extracted contents not directory | ZIP contains `rtw-style-loading-screens/` — extract parent |
| Game not restarted | Restart game after installation |
| GOG / Microsoft Store path different | Find correct mod directory for platform |

**Platform Mod Directories**:
- **Steam (Windows)**: `C:\Program Files (x86)\Steam\steamapps\common\Imperator Rome\mod\`
- **Steam (Linux)**: `~/.steam/steam/steamapps/common/Imperator Rome/mod/`
- **GOG**: `~/GOG Games/Imperator Rome/mod/`
- **Microsoft Store**: `%USERPROFILE%\AppData\Local\Packages\ParadoxInteractive...` (complex; use Workshop)

## Diagnostic Commands

### Verify Mod Structure

```bash
# From repository root
find rtw-style-loading-screens -type f | sort
# Expected:
# rtw-style-loading-screens/descriptor.mod
# rtw-style-loading-screens/gfx/loadingscreens/load_0.dds
# ... load_1.dds through load_9.dds
# rtw-style-loading-screens/thumbnail.png
```

### Verify Descriptor Content

```bash
cat rtw-style-loading-screens/descriptor.mod
# Must contain valid Clausewitz with all 5 fields
```

### Verify DDS Files (Linux/macOS with ImageMagick)

```bash
# Check resolution and format
for f in rtw-style-loading-screens/gfx/loadingscreens/*.dds; do
  identify -format "%f: %wx%h %m\n" "$f"
done
# All should show: 3840x2160 DDS
```

### Check Game Logs

**Location**: `<game>/logs/game.log` or similar

**Search for**:
- `Loading mod: rtw-style-loading-screens`
- `Mounting mod path: mod/rtw-style-loading-screens`
- `Failed to load texture: gfx/loadingscreens/load_N.dds`

## Escalation

| Issue Level | Action |
|-------------|--------|
| Single asset corrupt | Re-export and patch release |
| Descriptor syntax error | Fix and patch release |
| Game version incompatibility | Update `supported_version`, test, major release |
| Workshop publishing broken | Check GitHub Actions logs; rotate Steam secrets if needed |
| Nexus page issues | Contact Nexus support; verify account access |

## Known Limitations (Not Bugs)

| Limitation | Workaround |
|------------|------------|
| Only 10 loading screens | Game engine limit |
| Random selection (no weighting) | Game engine limit |
| No config for players | Mod API limit |
| Workshop auto-update only for Steam users | Nexus/GitHub manual |
| No Linux/macOS native testing | Proton/Steam Play works |

## Reporting Issues

When reporting, include:
1. Game version (launcher bottom-left)
2. Mod version (launcher mod list)
3. Installation method (Workshop / Nexus / Manual)
4. OS and platform (Steam/GOG/MS Store)
5. Screenshot of issue
6. `game.log` relevant lines
7. Steps to reproduce
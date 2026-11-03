# Error Handling

## Overview

This mod has **no runtime error handling** because it contains no executable code. Errors manifest as asset loading failures in the host game (Imperator: Rome).

## Error Taxonomy

| Category | Source | Manifestation | Recovery |
|----------|--------|---------------|----------|
| **Descriptor Parse Error** | Game mod loader | Mod disabled; launcher shows error | Fix `descriptor.mod` syntax |
| **Version Incompatibility** | Game mod loader | Mod marked "Incompatible" in launcher | Update `supported_version` |
| **Missing Asset** | Game VFS / renderer | Vanilla loading screen shown (silent fallback) | Add missing `load_N.dds` |
| **Corrupt Asset** | Game texture loader | Purple/magenta checkerboard texture | Re-export valid DDS |
| **Wrong Format** | Game texture loader | Purple texture or crash | Export as DDS BC3/DXT5 |
| **Wrong Resolution** | Game renderer | Stretched/squished display | Re-export at 3840×2160 |
| **Mount Failure** | Game mod loader | Mod assets not available | Check `descriptor.mod` path field |
| **Workshop Sync Failure** | Steam client | Mod not updated | Restart Steam; check Actions log |

## Handling Patterns

### Silent Fallback (Vanilla)

**Behaviour**: Game silently falls back to vanilla loading screen if mod asset missing.

**Implication**: No error log, no user notification. Player may not notice.

**Detection**: Manual verification — observe loading screens for vanilla art.

**Prevention**: File count check (10 files) in release checklist.

### Visual Artefact (Corrupt DDS)

**Behaviour**: GPU renders magenta checkerboard (missing texture pattern).

**Implication**: Obvious to player; breaks immersion.

**Detection**: Visual inspection in-game; image editor validation pre-release.

**Prevention**: Open each DDS in GIMP/paint.net before commit.

### Launcher Error (Descriptor)

**Behaviour**: Launcher shows mod as "Incompatible" or missing.

**Implication**: Player cannot enable mod.

**Detection**: Launcher UI; game logs.

**Prevention**: Validate `descriptor.mod` syntax; test on target game version.

## Game Log Analysis

**Log Location**: `<game>/logs/game.log` (varies by platform)

**Relevant Patterns**:

```
# Successful mount
[Mod] Loading mod: rtw-style-loading-screens
[Mod] Mounting path: mod/rtw-style-loading-screens
[VFS] Mounted: mod/rtw-style-loading-screens/gfx/

# Missing asset (silent)
[Texture] Requested: gfx/loadingscreens/load_3.dds
[Texture] Fallback: vanilla/gfx/loadingscreens/load_3.dds

# Corrupt asset
[Texture] Failed to load: gfx/loadingscreens/load_5.dds
[Texture] Error: Invalid DDS header
[Renderer] Missing texture: load_5.dds
```

## Recovery Procedures

### Player-Side Recovery

| Issue | Player Action |
|-------|---------------|
| Mod incompatible | Wait for mod update; check Workshop/Nexus |
| Vanilla screens showing | Reinstall mod; verify file count |
| Purple textures | Reinstall mod; verify game integrity (Steam) |
| Mod not in launcher | Check mod directory placement; restart game |

### Maintainer-Side Recovery

| Issue | Maintainer Action |
|-------|-------------------|
| Corrupt asset shipped | Patch release with fixed asset |
| Wrong supported_version | Patch release with corrected descriptor |
| Workshop publish failed | Fix workflow; re-run or manual upload |
| Nexus upload failed | Retry upload; check file size/format |

## Error Prevention

### Pre-Release Validation

```bash
# 1. File count
ls rtw-style-loading-screens/gfx/loadingscreens/load_*.dds | wc -l
# Must be 10

# 2. Descriptor syntax
cat rtw-style-loading-screens/descriptor.mod
# Validate all 5 fields present

# 3. Asset validity (manual)
# Open each DDS in GIMP/paint.net
# Verify: 3840x2160, BC3/DXT5, no artefacts

# 4. In-game test
# Copy to game mod dir, launch, verify all 10 screens
```

### CI Validation (Future)

| Check | Tool | Integration |
|-------|------|-------------|
| DDS header validation | Custom script / `dds-tools` | GitHub Actions |
| Resolution enforcement | ImageMagick `identify` | GitHub Actions |
| Descriptor syntax | Clausewitz parser | GitHub Actions |
| File count | Shell | GitHub Actions |

## Monitoring

**No automated monitoring.** Relies on:
- Player reports (GitHub issues, Workshop comments)
- Maintainer visual verification per release
- Steam Workshop update notifications

## Escalation

| Severity | Response |
|----------|----------|
| Single asset corrupt | Patch release (PATCH version) |
| All assets corrupt | Emergency patch; investigate export pipeline |
| Descriptor breaks mod | Immediate patch; test before tag |
| Game API change | Major version update; full re-test |
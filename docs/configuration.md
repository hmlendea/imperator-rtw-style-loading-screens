# Configuration

## Overview

This mod has **no runtime configuration**. It is a static asset replacement with no executable code, no settings file, no user preferences, and no environment variables.

All configuration is **build-time / release-time** and embedded in static files.

## Configuration Sources

### 1. Mod Descriptor (`descriptor.mod`)

**Location**: `rtw-style-loading-screens/descriptor.mod`

**Format**: Clausewitz script (Paradox proprietary)

**Fields**:

| Field | Current Value | Purpose | Mutable |
|-------|---------------|---------|---------|
| `version` | `v1.0.0` | Mod version displayed in launcher | Per release |
| `tags` | `["Interface"]` | Launcher category filter | Rarely |
| `name` | `rtw-style-loading-screens` | Internal mod identifier | Never (breaks path) |
| `supported_version` | `2.0.*` | Game version compatibility | On game major update |
| `path` | `mod/rtw-style-loading-screens` | Virtual filesystem mount path | Never (breaks mount) |

**Precedence**: Game mod loader reads this file at startup. No override mechanism.

### 2. Asset File Set

**Location**: `rtw-style-loading-screens/gfx/loadingscreens/`

**Configuration**: Fixed set of 10 files (`load_0.dds` … `load_9.dds`)

**Mutable**: Only by replacing files (no add/remove without game API change)

### 3. Distribution Workflow (`.github/workflows/steam-workshop.yml`)

**Location**: `.github/workflows/steam-workshop.yml`

**Fields**:

| Field | Value | Purpose |
|-------|-------|---------|
| `appid` | `859580` | Imperator: Rome Steam App ID |
| `itemid` | `3513150774` | Workshop item ID (immutable) |
| `changenote` | BBCode template | Release notes format |
| `path` | `rtw-style-loading-screens/` | Directory to upload |

**Secrets** (GitHub repository secrets):
- `STEAM_USERNAME`
- `STEAM_PASSWORD`
- `STEAM_2FASEED`

## Configuration Precedence

No precedence hierarchy exists. Single source per concern:

| Concern | Source | Override |
|---------|--------|----------|
| Mod identity | `descriptor.mod` | None |
| Game compatibility | `descriptor.mod` `supported_version` | None |
| Asset content | DDS files | None |
| Workshop publishing | Workflow YAML + secrets | None |

## Runtime Configuration

**None.** The mod exposes no settings to players. Players cannot:
- Select which loading screens display
- Change resolution
- Adjust art style
- Enable/disable individual screens
- Configure randomisation

The game's loading screen selection (random from 0-9) is hardcoded in the game engine.

## Environment-Specific Configuration

**None.** The mod behaves identically across:
- Operating systems (Windows, macOS, Linux via Steam/Proton)
- Game installations (Steam, GOG, Microsoft Store)
- Distribution channels (Workshop, Nexus, manual)

## Configuration Validation

| Configuration | Validation Method | Failure Mode |
|---------------|-------------------|--------------|
| `descriptor.mod` syntax | Game mod loader parse | Mod disabled |
| `supported_version` match | Game mod loader version check | Mod marked incompatible |
| DDS file validity | Game texture loader | Purple/missing texture |
| DDS resolution | Visual inspection | Stretched display |
| Filename exactness | Game VFS lookup | Vanilla fallback |

## Change Management

### Safe Changes

- Replace DDS files (same names, same format)
- Update `version` field
- Update `thumbnail.png`
- Update workflow `changenote` template

### Dangerous Changes

- Rename `descriptor.mod` fields
- Change `name` field (breaks mount path)
- Change `path` field (breaks mount)
- Change `supported_version` incorrectly (breaks compatibility)
- Add/remove DDS files (no effect or vanilla fallback)

### Forbidden Changes

- Change Workshop `itemid` (orphans subscribers)
- Change Nexus Mods ID (orphans users)
- Change `appid` (breaks Workshop upload)

## Configuration Discovery

For future agents:

1. **Mod metadata**: `rtw-style-loading-screens/descriptor.mod`
2. **Asset inventory**: `ls rtw-style-loading-screens/gfx/loadingscreens/`
3. **Publishing config**: `.github/workflows/steam-workshop.yml`
4. **Secrets**: GitHub repository settings (not in repo)
5. **Distribution IDs**: README badges, workflow `itemid`, Nexus page

No other configuration exists. No `.env` files, no JSON/YAML config, no registry, no database.
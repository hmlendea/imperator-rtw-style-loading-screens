# Architecture (Documentation Complement)

This document complements the root [ARCHITECTURE.md](../ARCHITECTURE.md) by elaborating on implementation details, component internals, and operational concerns not fully covered in the root document. It does not duplicate root content.

## Component Deep Dives

### Mod Descriptor (`descriptor.mod`)

**Location**: `rtw-style-loading-screens/descriptor.mod`

**Format**: Clausewitz script (Paradox proprietary format)

**Content**:
```clausewitz
version="v1.0.0"
tags={
    "Interface"
}
name="rtw-style-loading-screens"
supported_version="2.0.*"
path="mod/rtw-style-loading-screens"
```

**Field Semantics**:

| Field | Purpose | Constraints |
|-------|---------|-------------|
| `version` | Mod version string displayed in launcher | Semantic versioning; updated per release |
| `tags` | Mod category for launcher filtering | Fixed: `["Interface"]` for UI/content mods |
| `name` | Internal mod identifier | Must match directory name; lowercase, hyphens |
| `supported_version` | Game version compatibility range | Semantic version wildcard; currently `2.0.*` |
| `path` | Virtual filesystem mount path | Must be `mod/<name>`; game mounts `gfx/` under this |

**Validation**: Game mod loader parses on startup. Malformed descriptor → mod disabled. Version mismatch → mod marked incompatible in launcher.

### Loading Screen Assets (`gfx/loadingscreens/load_0.dds` … `load_9.dds`)

**Location**: `rtw-style-loading-screens/gfx/loadingscreens/`

**Format**: DDS (DirectDraw Surface) with BC3/DXT5 compression

**Specifications**:
- Resolution: 3840×2160 (4K)
- Colour space: sRGB
- Mipmaps: Typically 1 (loading screens displayed full-screen)
- Compression: BC3/DXT5 (alpha channel for potential UI overlays)

**Naming Contract**: Exact filenames `load_0.dds` through `load_9.dds`. Game selects randomly from this fixed set during loading sequences. Adding files has no effect; removing files causes vanilla fallback for missing indices.

**Art Direction**: Rome: Total War (2004) aesthetic — painterly, historical illustration style, muted earth tones, Roman military and civic imagery.

### Thumbnail (`thumbnail.png`)

**Location**: `rtw-style-loading-screens/thumbnail.png`

**Format**: PNG, typically 1280×720 (16:9)

**Purpose**: Displayed in game launcher mod list and Steam Workshop page. No functional role in game runtime.

## Runtime Flow Detail

### Mod Loading Sequence

```
1. Game process starts
2. Mod loader enumerates mod directories
3. For each mod directory:
   a. Reads descriptor.mod
   b. Parses supported_version against current game version
   c. If compatible: mounts path="mod/rtw-style-loading-screens" → gfx/
4. Virtual filesystem now serves mod assets with precedence over vanilla
5. Game enters main menu / loads save
6. Loading screen triggered (new game, load game, map transition)
7. Game requests gfx/loadingscreens/load_N.dds (N = 0-9)
8. VFS returns mod's DDS (mount precedence)
9. GPU texture upload → render
```

### Asset Resolution Precedence

The game's virtual filesystem uses mount order precedence. Mods loaded later override earlier mods. This mod's mount path `mod/rtw-style-loading-screens` gives it standard precedence. If multiple loading screen mods enabled, last-loaded wins per file.

## Architectural Areas

### Mod Root (`rtw-style-loading-screens/`)

**Boundaries**:
- Single directory = single deployable unit
- No sub-modules or optional components
- All files required for functional mod

**Invariants**:
- `descriptor.mod` at root
- `gfx/loadingscreens/` with exactly 10 DDS files
- `thumbnail.png` at root

### Loading Screen Assets (`gfx/loadingscreens/`)

**Boundaries**:
- Fixed filename set (`load_0.dds` … `load_9.dds`)
- Fixed format (DDS)
- Fixed count (10)

**Extension Constraints**:
- Cannot add loading screens without game API change
- Cannot randomise or weight selection (game chooses)
- Cannot implement conditional loading (no scripting)

## Data Architecture

### Asset Pipeline

```
Source Artwork (PSD/KRA/XCF)
    → Export 4K DDS (BC3/DXT5)
    → Commit to gfx/loadingscreens/
    → Tag release (v*.*.*)
    → GitHub Actions packages ZIP
    → Steam Workshop / Nexus / GitHub Releases
    → Player extracts to mod directory
    → Game VFS mounts
    → GPU texture upload at runtime
```

### Immutability

All assets immutable per release version. No runtime modification, no caching layer, no generated content. Updates replace entire file set.

## Interfaces and Integrations

### Imperator: Rome Mod Loader (Inbound)

**Contract**: `descriptor.mod` format, `supported_version` semantic matching, mount path convention

**Failure Modes**:
- Descriptor parse error → mod ignored
- Version mismatch → mod disabled in launcher
- Mount path conflict → undefined behaviour (last mount wins)

### Game Virtual Filesystem (Inbound)

**Contract**: `gfx/loadingscreens/load_0.dds` … `load_9.dds` valid DDS

**Failure Modes**:
- Missing file → vanilla fallback (silent)
- Corrupt DDS → render error (purple/missing texture)
- Wrong dimensions → stretched/squished display

### Steam Workshop (Outbound)

**Contract**: ZIP with `rtw-style-loading-screens/` at root; BBCode changenote

**Workflow**: `.github/workflows/steam-workshop.yml` triggered on `v*.*.*` tags

**Authentication**: `STEAM_USERNAME`, `STEAM_PASSWORD`, `STEAM_2FASEED` secrets

**Item ID**: `3513150774` (immutable)

### Nexus Mods (Outbound)

**Contract**: Manual or automated upload; metadata via Nexus UI

**Item ID**: `25` (Imperator: Rome category)

### GitHub Releases (Outbound)

**Contract**: ZIP attached to git tag `v*.*.*`

**Automation**: Implicit via GitHub release creation (workflow downloads from release)

## Dependency Direction

```
Imperator: Rome (game)
    └── reads → descriptor.mod
    └── mounts → gfx/loadingscreens/
    └── loads → load_N.dds

Distribution Channels
    └── consume → release ZIP
    └── publish → Workshop / Nexus / Releases
```

No reverse dependencies. Mod does not call game APIs, does not hook events, does not extend game systems.

## Deployment and Operations

### Release Process

1. Prepare assets in `gfx/loadingscreens/`
2. Update `descriptor.mod` version
3. Commit and tag: `git tag v1.0.1 && git push --tags`
4. GitHub Actions:
   - Creates GitHub Release with ZIP
   - Downloads ZIP from release
   - Uploads to Steam Workshop via `hmlendea/steam-workshop-update`
5. Manual: Upload same ZIP to Nexus Mods

### Rollback

Delete tag → GitHub Actions does not re-run. Existing Workshop/Nexus versions unaffected. Players on old version continue working.

### Monitoring

No automated monitoring. Verification: manual in-game visual check post-release.

## Compatibility Contracts

| Contract | Current Value | Change Trigger | Verification |
|----------|---------------|----------------|--------------|
| `supported_version` | `2.0.*` | Game major version update | Launch game, check launcher compatibility |
| Filename set | `load_0.dds` … `load_9.dds` | Game mod API change | File existence check |
| DDS format | BC3/DXT5, 4K | Game rendering pipeline change | In-game visual verification |
| Workshop Item ID | `3513150774` | Never (would orphan subscribers) | N/A |
| Nexus Mods ID | `25` | Never | N/A |

## Design Constraints (Elaborated)

### Vanilla Asset Parity

Game engine selects loading screen by index (0-9) at random. Mod must provide all 10 indices. Partial replacement → mixed vanilla/mod screens. No mechanism to disable specific vanilla screens.

### DDS Format Lock-in

Game texture loader only supports DDS for loading screens. PNG/JPG ignored. Conversion pipeline required for artists using other formats.

### Game Version Coupling

`supported_version="2.0.*"` uses Clausewitz version matching. Major version (3.0) breaks compatibility. Minor/patch (2.1, 2.0.4) compatible. Update requires descriptor edit + testing.

### No Code Extension Points

Mod system provides no hooks for:
- Weighted random selection
- Context-aware loading screens (campaign, scenario, multiplayer)
- Animated or video loading screens
- Player-preference configuration

Pure 1:1 static replacement only.

### Distribution Channel Immutability

Steam Workshop item ID assigned at creation. Cannot transfer subscribers to new ID. Nexus Mods similar. GitHub Releases tied to git tags (immutable once pushed).

## Extension Points (Future)

If game mod API evolves:

1. **Weighted Loading Screens**: Would require game support for metadata file (e.g., `loadingscreens.json` with weights)
2. **Conditional Loading**: Would require scripting API (Lua/C#) in mod system
3. **Additional Screens**: Would require game to scan directory instead of fixed indices
4. **Animated Screens**: Would require video codec support in loading screen renderer

Current architecture: none of the above possible.
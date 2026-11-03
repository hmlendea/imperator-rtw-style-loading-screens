# State and Persistence

## Overview

This mod has **no state and no persistence**. It is a static asset replacement with no runtime, no configuration, no user data, and no storage.

## State Model

**None.** No application state exists.

| State Type | Present | Notes |
|------------|---------|-------|
| Runtime state | No | No code executes |
| Configuration state | No | No settings file |
| User state | No | No player data |
| Session state | No | No sessions |
| Cache state | No | No caching layer |
| Database state | No | No database |

## Persistence Model

**None.** No data is persisted by the mod.

| Persistence Concern | Status |
|---------------------|--------|
| Database | None |
| File storage | Static assets only (immutable per release) |
| Cache | None |
| Migration | None |
| Backup | None |
| Recovery | None |

## Asset Lifecycle

The only "state" is the static asset set, which follows a simple lifecycle:

```
Creation (artist export)
    -> Commit (git)
    -> Tag (release)
    -> Package (ZIP)
    -> Distribute (Workshop/Nexus/GitHub)
    -> Install (player filesystem)
    -> Load (game VFS, runtime only)
    -> Render (GPU, runtime only)
```

### Immutability

- Assets immutable per release version
- Updates replace entire file set (no partial updates)
- No runtime modification of assets
- No generated content

## Player-Side State

The mod creates no player-side state beyond the installed files:

| Location | Content | Managed By |
|----------|---------|------------|
| `<game>/mod/rtw-style-loading-screens/` | Mod files | Steam Workshop / Player |
| Steam Workshop cache | Downloaded ZIP | Steam client |
| Nexus Mods manager | Downloaded ZIP | Nexus client |

The mod does not read, write, or modify any of these locations at runtime.

## Game-Side State

The game (Imperator: Rome) maintains its own state:

| State | Owner | Mod Interaction |
|-------|-------|-----------------|
| Mod enabled/disabled | Game launcher | Player toggles |
| VFS mount table | Game mod loader | Read-only (mod provides files) |
| Loaded textures | Game renderer | Read-only (GPU upload) |
| Loading screen selection | Game engine | Random index 0-9 (hardcoded) |

The mod has no access to game state.

## Failure and Recovery

| Failure | State Impact | Recovery |
|---------|--------------|----------|
| Corrupt asset | None (static file) | Reinstall mod |
| Missing asset | None (vanilla fallback) | Reinstall mod |
| Descriptor error | None (mod disabled) | Fix descriptor; reinstall |
| Game crash | None (mod not involved) | Game-level recovery |

No state corruption possible because no state exists.

## Migration

**Not applicable.** No schema, no data format evolution, no migration scripts.

## Backup

**Not applicable.** No user data to back up. Mod files are re-downloadable from distribution channels.

## Future State Considerations

If the mod ever gains executable components (unlikely per design), state management would require:
- Configuration file format and location
- Player preference storage
- Cache invalidation strategy
- Migration path for format changes

Current architecture: none of the above required.
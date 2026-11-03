# Repository Overview

## Purpose

This repository contains a content-only mod for Imperator: Rome that replaces the game's vanilla loading screens with artwork matching the art style of Rome: Total War (2004). The mod consists entirely of static DDS image files and a mod descriptor; it contains no executable code, runtime logic, or network communication.

## Scope

The repository owns:

- Ten DDS loading screen images (`load_0.dds` through `load_9.dds`) at 4K resolution
- A mod descriptor (`descriptor.mod`) declaring metadata and game version compatibility
- A thumbnail image (`thumbnail.png`) for launcher and Workshop display
- Distribution pipeline configuration (GitHub Actions workflow for Steam Workshop)
- Project documentation and governance files (README, CONTRIBUTING, CODE_OF_CONDUCT, PRIVACY, SECURITY, LICENSE)

The repository deliberately does not own:

- Game executable or runtime
- Mod loader implementation
- Distribution channel infrastructure (Steam Workshop, Nexus Mods, GitHub Releases)
- Player installation or game configuration

## Principal Capabilities

1. **Asset Replacement**: Provides ten RTW-style loading screen images that override vanilla assets via the game's native mod loader
2. **Version Compatibility**: Declares `supported_version="2.0.*"` in descriptor for Imperator: Rome 2.0.x compatibility
3. **Multi-Channel Distribution**: Publishes to Steam Workshop, Nexus Mods, and GitHub Releases via tagged releases
4. **Zero-Runtime Operation**: No code execution, configuration, or external dependencies beyond base game

## Component Relationships

```
descriptor.mod (metadata, mount path)
    └── gfx/loadingscreens/
        ├── load_0.dds … load_9.dds (replacement assets)
        └── thumbnail.png (launcher/Workshop image)
```

## Dependency Direction

External dependencies only (game reads mod):
- Imperator: Rome mod loader → reads `descriptor.mod` and mounts `gfx/`
- Game virtual filesystem → serves `load_N.dds` from mod path with precedence over vanilla
- Distribution channels → consume release ZIP artefacts

## Runtime Topology

Single-player client-side only. Mod assets loaded into game process memory during loading screen transitions. No persistent processes, services, or network connections.

## Major State

None. All assets are immutable static files per release version.

## Major Integrations

- Steam Workshop (item ID: 3513150774)
- Nexus Mods (ID: 25 for Imperator: Rome)
- GitHub Releases (tagged `v*.*.*`)

## System-Wide Invariants

1. Exactly ten loading screen files named `load_0.dds` through `load_9.dds`
2. All files in DDS format compatible with Imperator: Rome rendering pipeline
3. 4K resolution (3840×2160) per project specification
4. `descriptor.mod` `supported_version` matches target game version
5. Steam Workshop and Nexus Mods item IDs remain stable across releases
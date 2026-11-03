# Quick Start

## For Players

### Install via Steam Workshop (Recommended)

1. Open [Steam Workshop page](https://steamcommunity.com/sharedfiles/filedetails/?id=3513150774)
2. Click **Subscribe**
3. Launch **Imperator: Rome**
4. Mod automatically downloads and enables
5. Play — loading screens now RTW-style

### Install Manually (GitHub / Nexus / GOG / Non-Steam)

1. Download latest `rtw-style-loading-screens.zip` from:
   - [GitHub Releases](https://github.com/hmlendea/imperator-rtw-style-loading-screens/releases)
   - [Nexus Mods](https://nexusmods.com/imperatorrome/mods/25)
2. Extract ZIP
3. Copy `rtw-style-loading-screens/` folder to game `mod/` directory:
   - **Steam Windows**: `C:\Program Files (x86)\Steam\steamapps\common\Imperator Rome\mod\`
   - **Steam Linux**: `~/.steam/steam/steamapps/common/Imperator Rome/mod/`
   - **GOG**: `~/GOG Games/Imperator Rome/mod/`
4. Launch game, enable mod in launcher
5. Play

### Verify Installation

1. Open Imperator: Rome launcher
2. Mods tab → "RTW-Style Loading Screens" should be listed
3. Version matches latest release
4. Thumbnail visible
5. Start game → loading screens show RTW artwork

## For Contributors

### Prerequisites

- Git
- Image editor with DDS support:
  - **GIMP** + [DDS plugin](https://github.com/nshafer/gimp-dds-plugin)
  - **paint.net** + [DDS plugin](https://forums.getpaint.net/topic/115233-dds-filetype-plugin/)
- Imperator: Rome installed (for testing)

### Development Setup

```bash
# Clone repository
git clone https://github.com/hmlendea/imperator-rtw-style-loading-screens.git
cd imperator-rtw-style-loading-screens

# Verify structure
ls rtw-style-loading-screens/gfx/loadingscreens/
# Should show: load_0.dds ... load_9.dds
```

### Making Changes

1. **Create artwork** at 3840×2160 (4K) in your editor
2. **Export as DDS** with BC3/DXT5 compression
3. **Replace** target `load_N.dds` in `rtw-style-loading-screens/gfx/loadingscreens/`
4. **Test in-game**:
   - Copy mod to game `mod/` directory
   - Launch game, trigger loading screens
   - Verify appearance
5. **Commit** with descriptive message
6. **Open Pull Request** targeting `main` branch

### Release Process (Maintainers Only)

```bash
# Update descriptor.mod version
# Commit changes
git tag v1.0.1
git push --tags
# GitHub Actions: packages ZIP, publishes to Steam Workshop
# Maintainer: manually upload same ZIP to Nexus Mods
```

## For Documentation Readers

### Start Here

| Goal | Document |
|------|----------|
| Understand project purpose | [Repository Overview](./repository-overview.md) |
| Navigate codebase | [Repository Structure](./repository-structure.md) |
| Learn architecture | [Architecture](./architecture.md) (complements root [ARCHITECTURE.md](../ARCHITECTURE.md)) |
| See data model | [Data Model](./data-model.md) |
| Understand build/deploy | [Build and Deployment](./build-and-deployment.md) |
| Configuration reference | [Configuration](./configuration.md) |
| Dependencies | [Dependencies](./dependencies.md) |
| Design rationale | [Design Decisions](./design-decisions.md) |
| Testing approach | [Testing](./testing.md) |
| Common issues | [Troubleshooting](./troubleshooting.md) |
| Quick answers | [FAQ](./faq.md) |

### Root Documents (Repository Root)

| Document | Purpose |
|----------|---------|
| [ARCHITECTURE.md](../ARCHITECTURE.md) | System architecture, components, flows, contracts |
| [README.md](../README.md) | Project overview, installation, links |
| [CONTRIBUTING.md](../CONTRIBUTING.md) | Contribution guidelines |
| [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) | Community standards |
| [PRIVACY.md](../PRIVACY.md) | Data handling (none) |
| [SECURITY.md](../SECURITY.md) | Vulnerability reporting |
| [LICENSE](../LICENSE) | GPL v3.0 terms |

## Key Facts at a Glance

| Property | Value |
|----------|-------|
| **Mod Name** | RTW-Style Loading Screens |
| **Game** | Imperator: Rome |
| **Supported Version** | 2.0.x |
| **Asset Count** | 10 loading screens + thumbnail |
| **Asset Format** | DDS (BC3/DXT5), 4K (3840×2160) |
| **Distribution** | Steam Workshop, Nexus Mods, GitHub Releases |
| **License** | GPL v3.0 |
| **Code** | None (content only) |
| **Configuration** | None (static) |
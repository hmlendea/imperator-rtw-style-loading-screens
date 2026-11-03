# Dependencies

## External Dependencies

### Runtime Dependencies

| Dependency | Version | Type | Purpose | Failure Impact |
|------------|---------|------|---------|----------------|
| **Imperator: Rome** | 2.0.x | Game executable | Host process; mod loader; rendering pipeline | Mod non-functional |
| **Imperator: Rome Mod Loader** | Built-in | Game subsystem | Reads `descriptor.mod`, mounts VFS | Mod ignored |
| **Game Virtual Filesystem** | Built-in | Game subsystem | Serves `gfx/loadingscreens/load_N.dds` with mod precedence | Vanilla fallback |
| **GPU / Graphics Driver** | Any supporting DDS/BC3 | Hardware/OS | Texture upload and rendering | Visual artefacts / crash |

### Distribution Dependencies

| Dependency | Type | Purpose | Failure Impact |
|------------|------|---------|----------------|
| **Steam Workshop** | CDN / API | Content delivery to Steam users | Workshop subscribers don't receive updates |
| **Nexus Mods** | CDN / Web UI | Content delivery to Nexus users | Nexus users don't receive updates |
| **GitHub Releases** | Artefact storage | Release ZIP hosting | Manual installers can't download |
| **GitHub Actions** | CI/CD | Automated Workshop publishing | Manual Workshop upload required |

### Development Dependencies

| Dependency | Version | Purpose | Required For |
|------------|---------|---------|--------------|
| **Image Editor with DDS Support** | GIMP + DDS plugin / paint.net + DDS plugin | Create/edit loading screen assets | Asset creation |
| **Git** | Any | Version control | All development |
| **GitHub CLI / Web UI** | Any | Release management | Manual Nexus upload, release creation |

## Internal Dependencies

**None.** The mod contains no code, no modules, no internal libraries, and no inter-file dependencies beyond the fixed asset set.

## Dependency Direction

```
Imperator: Rome (game)
    ├── Mod Loader → reads → descriptor.mod
    ├── VFS → mounts → gfx/loadingscreens/
    └── Renderer → loads → load_N.dds

Distribution Channels
    ├── Steam Workshop ← publishes ← GitHub Actions
    ├── Nexus Mods ← uploads ← Maintainer
    └── GitHub Releases ← creates ← Git tag push
```

No reverse dependencies. The game does not depend on this mod. Distribution channels do not depend on each other.

## Version Compatibility Matrix

| Mod Version | Game Version | Workshop | Nexus | GitHub Releases |
|-------------|--------------|----------|-------|-----------------|
| 1.0.0 | 2.0.* | ✅ | ✅ | ✅ |
| Future | 2.0.* | ✅ | ✅ | ✅ |
| Future | 3.0.* | ❓ (requires descriptor update) | ❓ | ❓ |

## Dependency Management

### No Package Manager

No `package.json`, `requirements.txt`, `Cargo.toml`, `.csproj`, or equivalent. All dependencies are:
- System-level (game installation)
- Platform-level (Steam, Nexus, GitHub)
- Tool-level (image editor)

### No Lockfile

No dependency lockfile exists. Version pinning:
- Game: via `descriptor.mod` `supported_version`
- Workshop: via immutable `itemid`
- Nexus: via immutable mod ID
- Tools: unspecified (any DDS-capable editor)

## Supply Chain

```
Artist → DDS Export → Git Commit → Tag → GitHub Actions → Steam Workshop
                                                      → GitHub Releases
Maintainer → Manual Upload → Nexus Mods
Player → Subscribe/Download → Game Mod Directory → Game VFS
```

### Trust Boundaries

| Boundary | Trust Assumption | Verification |
|----------|------------------|--------------|
| Artist → Repo | Maintainer reviews PRs | Visual inspection |
| Repo → GitHub Actions | GitHub infrastructure | Signed commits |
| GitHub Actions → Steam | Steam API + secrets | Workshop item ID match |
| Steam → Player | Steam CDN integrity | Steam client verification |
| Nexus → Player | Nexus CDN integrity | Nexus client verification |
| GitHub Releases → Player | GitHub infrastructure | HTTPS + checksums |

## Vulnerability Surface

| Vector | Likelihood | Impact | Mitigation |
|--------|------------|--------|------------|
| Malicious DDS in PR | Low (reviewed) | Texture exploit | Maintainer review |
| Compromised Steam secrets | Low (secrets) | Workshop hijack | Rotate secrets |
| Compromised Nexus account | Low | Nexus page hijack | 2FA on Nexus |
| Supply chain (GitHub Actions) | Low | Malicious workflow | Pinned action versions |

## Dependency Updates

| Dependency | Update Trigger | Process |
|------------|----------------|---------|
| Game major version | Game update | Update `supported_version`, test, release |
| Steam Workshop API | Valve changes | Update workflow action version |
| Nexus Mods API | Nexus changes | Manual upload adaptation |
| DDS format support | Game engine change | Re-export assets |

No automated dependency scanning (Dependabot, Renovate, etc.) applicable.
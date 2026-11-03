# Integrations

## Overview

This mod integrates with three external systems: the Imperator: Rome game engine (via its mod loader), Steam Workshop, and Nexus Mods. GitHub Releases serves as the artefact store and release trigger.

## Integration Matrix

| Integration | Direction | Protocol | Authentication | Frequency |
|-------------|-----------|----------|----------------|-----------|
| Imperator: Rome Mod Loader | Inbound (game reads mod) | Clausewitz script + VFS | None (local) | Game launch |
| Steam Workshop | Outbound (publish) | Steam Web API | Steam credentials (secrets) | Per release tag |
| Nexus Mods | Outbound (publish) | Web UI / API | Nexus account | Per release (manual) |
| GitHub Releases | Outbound (artefact) | GitHub API | GitHub token (implicit) | Per release tag |

## Imperator: Rome Mod Loader

### Contract

| Aspect | Specification |
|--------|---------------|
| **Descriptor Format** | Clausewitz script (Paradox proprietary) |
| **Required Fields** | `version`, `tags`, `name`, `supported_version`, `path` |
| **Mount Path** | `mod/rtw-style-loading-screens` → `gfx/` |
| **Asset Path** | `gfx/loadingscreens/load_0.dds` … `load_9.dds` |
| **Version Matching** | Semantic version wildcard (`2.0.*`) |
| **Precedence** | Mod mount order; last wins for conflicts |

### Failure Modes

| Failure | Game Behaviour | Player Impact |
|---------|----------------|---------------|
| Descriptor parse error | Mod ignored | Mod not in launcher |
| Version mismatch | Mod marked incompatible | Cannot enable |
| Mount path conflict | Undefined (last wins) | Wrong assets loaded |
| Missing asset | Silent vanilla fallback | Vanilla screen shows |
| Corrupt asset | Purple texture | Visual artefact |

### Testing Integration

```bash
# Manual: install mod, launch game, check launcher, trigger loads
# Automated: none
```

## Steam Workshop

### Contract

| Aspect | Specification |
|--------|---------------|
| **App ID** | `859580` (Imperator: Rome) |
| **Item ID** | `3513150774` (immutable) |
| **Artefact** | ZIP with `rtw-style-loading-screens/` at root |
| **Changenote** | BBCode with release URL |
| **Visibility** | Public |
| **Update Method** | `hmlendea/steam-workshop-update` GitHub Action |

### Workflow

```yaml
# .github/workflows/steam-workshop.yml
on:
  push:
    tags:
    - 'v*.*.*'

jobs:
  release:
    steps:
    - name: Prepare
      run: |
          wget "https://github.com/.../releases/download/${{github.ref_name}}/rtw-style-loading-screens_${GITHUB_REF:11}.zip" -O "rtw-style-loading-screens.zip"
          unzip "rtw-style-loading-screens.zip" -d "rtw-style-loading-screens/"
    - name: Upload
      uses: hmlendea/steam-workshop-update@latest
      with:
        appid: '859580'
        itemid: '3513150774'
        path: "rtw-style-loading-screens/"
        changenote: "[url=...]Version ${{github.ref_name}}[/url]"
```

### Secrets

| Secret | Purpose | Rotation |
|--------|---------|----------|
| `STEAM_USERNAME` | Steam account login | On compromise |
| `STEAM_PASSWORD` | Steam account password | On compromise |
| `STEAM_2FASEED` | 2FA shared secret (headless) | On compromise |

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Auth failed | Wrong secrets | Rotate secrets; re-run workflow |
| Item ID invalid | Changed ID | **Never change ID** |
| ZIP corrupt | Download failed | Re-run workflow |
| Rate limited | Steam API limits | Wait; re-run |

### Monitoring

- GitHub Actions workflow status
- Workshop page version number
- Subscriber count (Workshop analytics)

## Nexus Mods

### Contract

| Aspect | Specification |
|--------|---------------|
| **Game** | Imperator: Rome |
| **Mod ID** | `25` (immutable) |
| **Category** | Interface / Visuals |
| **Artefact** | Same ZIP as Workshop |
| **Metadata** | Version, changelog, tags via web UI |
| **Update Method** | Manual upload |

### Process

1. GitHub Actions creates Release with ZIP
2. Maintainer downloads ZIP from Releases
3. Maintainer uploads to Nexus via web UI
4. Maintainer updates version/changelog on Nexus page

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Upload failed | Network / file size | Retry; check limits |
| Version mismatch | Forgot to update | Edit Nexus page |
| Category wrong | UI error | Edit Nexus page |

### Automation Gap

**No automated Nexus upload.** Requires manual step per release. Future improvement: Nexus API integration.

## GitHub Releases

### Contract

| Aspect | Specification |
|--------|---------------|
| **Trigger** | Git tag `v*.*.*` |
| **Artefact** | ZIP of `rtw-style-loading-screens/` |
| **Release Notes** | Auto-generated from commits |
| **Visibility** | Public |

### Role

- Primary artefact store for Workshop workflow (downloads ZIP from here)
- Manual install source for players
- Source archive for transparency

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Release not created | Tag format wrong | Use `v*.*.*` format |
| ZIP missing | Actions artifact failed | Check Actions logs |

## Integration Dependencies

```
Git Tag (v*.*.*)
    → GitHub Release (auto)
    → GitHub Actions (triggered)
        → Download Release ZIP
        → Steam Workshop Upload (auto)
    → Maintainer (manual)
        → Download Release ZIP
        → Nexus Mods Upload (manual)
```

## Version Synchronisation

All channels must receive identical ZIP artefact per version:

| Channel | Version Source | Sync Method |
|---------|----------------|-------------|
| GitHub Release | Git tag | Automatic |
| Steam Workshop | Git tag → Actions | Automatic |
| Nexus Mods | Git tag → Manual | Manual |

**Invariant**: Same ZIP content across all channels for a given version.

## Security Considerations

| Integration | Risk | Mitigation |
|-------------|------|------------|
| Steam Workshop | Credential compromise | Secrets in GitHub; rotate on suspicion |
| Nexus Mods | Account compromise | 2FA on Nexus; limited maintainer access |
| GitHub Releases | Supply chain | Signed commits; pinned Actions |
| Game Mod Loader | Malicious asset | PR review; maintainer approval |

## Future Integrations

| Integration | Status | Notes |
|-------------|--------|-------|
| Paradox Mods (official) | Not available | Imperator uses Steam Workshop |
| Mod.io | Not applicable | Paradox games use Workshop |
| Automated Nexus upload | Planned | Requires Nexus API token |
| Discord notifications | Deferred | Low priority |
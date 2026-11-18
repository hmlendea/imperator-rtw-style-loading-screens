# Integrations

## Overview

This mod integrates with three external systems: the Imperator: Rome game engine (via its mod loader), Steam Workshop, and Nexus Mods. GitHub Releases serves as the artefact store and release trigger.

## Integration Matrix

| Integration | Direction | Protocol | Authentication | Frequency |
|-------------|-----------|----------|----------------|-----------|
| Imperator: Rome Mod Loader | Inbound (game reads mod) | Clausewitz script + VFS | None (local) | Game launch |
| Steam Workshop | Outbound (publish) | Steam Web API | Steam credentials (secrets) | Per release tag |
| Nexus Mods | Outbound (publish) | Nexus Mods API (upload action) | Nexus API key (secret) | Per release tag |
| GitHub Releases | Outbound (artefact) | GitHub API | GitHub token (implicit) | Per release publication |

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
# .github/workflows/steam-workshop-release.yml
on:
  push:
    tags:
    - 'v*.*.*'
  workflow_dispatch:
    inputs:
      tag:
        description: 'Tag to release'
        required: true

jobs:
  release:
    steps:
    - uses: actions/checkout@v7
      with:
        ref: ${{ env.VERSION }}
    - name: Update the mod version
      run: |
        MOD_VERSION="${VERSION#v}"
        # set version= in both descriptor files
    - name: Build the release archive
      run: |
        zip -r "release.zip" "rtw-style-loading-screens/" "rtw-style-loading-screens.mod"
    - name: Extract the release archive
      run: |
        unzip "release.zip" -d "release/"
    - name: Upload
      uses: hmlendea/steam-workshop-update@latest
      with:
        appid: 859580
        itemid: 3513150774
        path: "release/rtw-style-loading-screens/"
        changenote: "[url=...]Version ${{env.VERSION}}[/url]"
```

### Secrets

| Secret | Purpose | Rotation |
|--------|---------|----------|
| `STEAM_USERNAME` | Steam account login | On compromise |
| `STEAM_PASSWORD` | Steam account password | On compromise |
| `STEAM_2FA_SEED` | 2FA shared secret (headless) | On compromise |

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Auth failed | Wrong secrets | Rotate secrets; re-run workflow |
| Item ID invalid | Changed ID | **Never change ID** |
| ZIP build failed | Checkout or archive error | Re-run workflow |
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
| **File ID** | `527539` (immutable) |
| **Category** | Interface / Visuals |
| **Artefact** | Same ZIP as Workshop |
| **Metadata** | Version, changelog set by the workflow |
| **Update Method** | `Nexus-Mods/upload-action` GitHub Action |

### Process

1. A `v*.*.*` tag push or manual dispatch starts `nexus-mods-release.yml`
2. The workflow checks out the tag and sets the descriptor version
3. It zips `rtw-style-loading-screens/` and `rtw-style-loading-screens.mod`
4. It uploads `rtw-style-loading-screens.zip` to Nexus Mods, archiving the previous version

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Upload failed | Network / invalid API key | Rotate key; re-run workflow |
| Version mismatch | Tag not checked out | Re-run with the correct `tag` input |
| Category wrong | Action input drift | Fix the workflow; re-run |

## GitHub Releases

### Contract

| Aspect | Specification |
|--------|---------------|
| **Trigger** | Published GitHub release |
| **Artefact** | `rtw-style-loading-screens_<version>.zip` of `rtw-style-loading-screens/` and `rtw-style-loading-screens.mod` |
| **Release Notes** | Written by the maintainer; left unmodified |
| **Visibility** | Public |

### Role

- Published by `github-release.yml` through `softprops/action-gh-release`
- Manual install source for players
- Source archive for transparency

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Release not created | Release not published | Publish the release for the tag |
| ZIP missing | Workflow failed | Check Actions logs; re-run the job |

## Integration Dependencies

```
Git Tag (v*.*.*)
    → GitHub Actions (triggered)
        → Steam Workshop Upload (auto)
        → Nexus Mods Upload (auto)
    → GitHub Release (published)
        → GitHub Release ZIP attachment (auto)
```

## Version Synchronisation

All channels receive the same mod content per version, each packaged independently from the checked-out tag:

| Channel | Version Source | Sync Method |
|---------|----------------|-------------|
| GitHub Release | Release tag | Automatic (`github-release.yml`) |
| Steam Workshop | Tag → Actions | Automatic (`steam-workshop-release.yml`) |
| Nexus Mods | Tag → Actions | Automatic (`nexus-mods-release.yml`) |

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
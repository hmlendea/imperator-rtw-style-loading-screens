# Build and Deployment

## Build Pipeline

**No build step exists.** The mod consists entirely of static assets committed directly to the repository. The only "build" is packaging the mod directory into a ZIP archive for distribution.

### Packaging Process

Triggered by GitHub Actions on version tags (`v*.*.*`):

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

**Artefact**: ZIP archive containing `rtw-style-loading-screens/` directory with:
- `descriptor.mod`
- `gfx/loadingscreens/load_0.dds` … `load_9.dds`
- `thumbnail.png`

## Release Process

### Automated (Steam Workshop)

1. Maintainer updates assets and `descriptor.mod` version
2. `git tag v1.0.1 && git push --tags`
3. GitHub Actions:
   - Creates GitHub Release (implicit via tag)
   - Downloads release ZIP from GitHub Releases
   - Uploads to Steam Workshop via `hmlendea/steam-workshop-update` action
   - Posts changenote with release URL

### Manual (Nexus Mods)

1. Same tag push creates GitHub Release with ZIP
2. Maintainer downloads ZIP from GitHub Releases
3. Uploads to Nexus Mods via web UI
4. Updates version metadata on Nexus page

### Manual (GitHub Releases)

Automatic: Git tag creates GitHub Release with ZIP attached.

## Deployment Environments

| Environment | Description | Deployment Method |
|-------------|-------------|-------------------|
| **Player Local** | Game mod directory on player's machine | Steam Workshop auto-update / Manual ZIP extract / Nexus Mods manager |
| **Steam Workshop** | Valve's content delivery network | GitHub Actions → Steam API |
| **Nexus Mods** | Nexus Mods CDN | Manual upload |
| **GitHub Releases** | GitHub's release artefact storage | Automatic on tag push |

## Configuration

### GitHub Actions Secrets

| Secret | Purpose | Required For |
|--------|---------|--------------|
| `STEAM_USERNAME` | Steam account login | Workshop upload |
| `STEAM_PASSWORD` | Steam account password | Workshop upload |
| `STEAM_2FASEED` | Steam 2FA shared secret | Workshop upload (headless) |

### Workflow Configuration

| Parameter | Value | Source |
|-----------|-------|--------|
| `appid` | `859580` | Imperator: Rome Steam App ID |
| `itemid` | `3513150774` | Workshop item ID (immutable) |
| `changenote` | BBCode with release URL | Template in workflow |

## Versioning

### Scheme

Semantic versioning: `vMAJOR.MINOR.PATCH` (e.g., `v1.0.0`, `v1.1.0`, `v1.0.1`)

### Tag Format

Git tags prefixed with `v`: `v*.*.*`

### Descriptor Version

`descriptor.mod` `version` field must match tag (without `v` prefix):
- Tag `v1.0.1` → `version="v1.0.1"`

### Game Compatibility

`descriptor.mod` `supported_version` field:
- Current: `2.0.*` (Imperator: Rome 2.0.x)
- Update when game major version changes

## Rollback Procedure

### Steam Workshop

1. Delete problematic tag: `git tag -d v1.0.1 && git push origin :refs/tags/v1.0.1`
2. Previous Workshop version remains available to subscribers
3. Re-tag with fix: `git tag v1.0.2 && git push --tags`

### Nexus Mods

1. Upload previous version ZIP via Nexus UI
2. Or upload fixed version as new release

### GitHub Releases

1. Delete release and tag via GitHub UI
2. Re-create with fix

## Verification

### Pre-Release Checklist

- [ ] All 10 DDS files present in `gfx/loadingscreens/`
- [ ] All DDS files valid (open in image viewer)
- [ ] All DDS files 4K resolution (3840×2160)
- [ ] `descriptor.mod` version matches intended tag
- [ ] `descriptor.mod` `supported_version` correct for target game version
- [ ] `thumbnail.png` present and valid
- [ ] No extra files in mod directory

### Post-Release Verification

1. Install mod via Workshop / Nexus / Manual
2. Launch Imperator: Rome with mod enabled
3. Trigger loading screens (new game / load save)
4. Verify all 10 screens display correctly (no missing textures, correct art)
5. Verify mod appears in launcher with correct version and thumbnail

## CI/CD Status

| Pipeline | Status | Notes |
|----------|--------|-------|
| Steam Workshop Deploy | Manual trigger (tag push) | Requires Steam secrets |
| Nexus Mods Deploy | Manual | No automation |
| GitHub Release | Automatic | On tag push |
| Automated Tests | None | Visual verification only |

## Known Limitations

- No automated DDS validation in CI
- No automated resolution check
- No automated descriptor validation
- Nexus Mods upload not automated
- No staging environment (direct to production channels)
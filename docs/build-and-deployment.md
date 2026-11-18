# Build and Deployment

## Build Pipeline

**No build step exists.** The mod consists entirely of static assets committed directly to the repository. The only "build" is packaging the mod directory into a ZIP archive for distribution.

### Packaging Process

Three workflows package and publish the mod. Each sets the descriptor version from the tag (leading `v` stripped) before archiving, and each archives `rtw-style-loading-screens/` together with `rtw-style-loading-screens.mod`.

```yaml
# .github/workflows/github-release.yml
on:
  release:
    types: [ published ]

# .github/workflows/nexus-mods-release.yml
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
```

The packaging and upload steps differ per channel:

```yaml
# github-release.yml
    - name: Extract the version from the GitHub tag
      id: version
      run: |
        VERSION="${{ github.event.release.tag_name }}"
        VERSION="${VERSION#v}"
        echo "version=${VERSION}" >> "$GITHUB_OUTPUT"

# nexus-mods-release.yml / steam-workshop-release.yml
    - name: Update the mod version
      run: |
        MOD_VERSION="${VERSION#v}"
```

```yaml
# .github/workflows/steam-workshop-release.yml (upload step)
    - name: Upload
      uses: hmlendea/steam-workshop-update@latest
      with:
        appid: 859580
        itemid: 3513150774
        path: "release/rtw-style-loading-screens/"
        changenote: "[url=...]Version ${{env.VERSION}}[/url]"
```

**Artefact**: ZIP archive containing `rtw-style-loading-screens/` directory with:
- `descriptor.mod`
- `gfx/loadingscreens/load_0.dds` ... `load_9.dds`
- `thumbnail.png`

plus `rtw-style-loading-screens.mod` at the archive root.

## Release Process

### Automated (GitHub Release)

1. Maintainer creates and publishes a GitHub release for a tag (for example `v1.0.1`)
2. `github-release.yml` runs on `release: types: [ published ]`:
   - Checks out the repository
   - Sets the descriptor version from the tag (leading `v` stripped)
   - Builds `rtw-style-loading-screens_<version>.zip`
   - Attaches the archive to the GitHub release

### Automated (Steam Workshop)

1. `git tag v1.0.1 && git push --tags` (or manual `workflow_dispatch` with the tag)
2. `steam-workshop-release.yml`:
   - Checks out the requested tag
   - Sets the descriptor version
   - Zips descriptor and content directory, then extracts the archive into `release/`
   - Uploads `release/rtw-style-loading-screens/` to Steam Workshop via `hmlendea/steam-workshop-update`
   - Posts changenote with release URL

### Automated (Nexus Mods)

1. Same `v*.*.*` tag push (or manual `workflow_dispatch` with the tag)
2. `nexus-mods-release.yml`:
   - Checks out the requested tag
   - Sets the descriptor version
   - Zips descriptor and content directory
   - Uploads `rtw-style-loading-screens.zip` to Nexus Mods via `Nexus-Mods/upload-action`, archiving the previous version

## Deployment Environments

| Environment | Description | Deployment Method |
|-------------|-------------|-------------------|
| **Player Local** | Game mod directory on player's machine | Steam Workshop auto-update / Manual ZIP extract / Nexus Mods manager |
| **Steam Workshop** | Valve's content delivery network | GitHub Actions → Steam API |
| **Nexus Mods** | Nexus Mods CDN | GitHub Actions → Nexus Mods API |
| **GitHub Releases** | GitHub's release artefact storage | Automatic on release publication |

## Configuration

### GitHub Actions Secrets

| Secret | Purpose | Required For |
|--------|---------|--------------|
| `STEAM_USERNAME` | Steam account login | Workshop upload |
| `STEAM_PASSWORD` | Steam account password | Workshop upload |
| `STEAM_2FA_SEED` | Steam 2FA shared secret | Workshop upload (headless) |
| `NEXUSMODS_API_KEY` | Nexus Mods upload API key | Nexus Mods upload |

### Workflow Configuration

| Parameter | Value | Source |
|-----------|-------|--------|
| `appid` | `859580` | Imperator: Rome Steam App ID |
| `itemid` | `3513150774` | Workshop item ID (immutable) |
| `file_id` | `527539` | Nexus Mods file ID (immutable) |
| `changenote` | BBCode with release URL | Template in workflow |

## Versioning

### Scheme

Semantic versioning: `vMAJOR.MINOR.PATCH` (e.g., `v1.0.0`, `v1.1.0`, `v1.0.1`)

### Tag Format

Git tags prefixed with `v`: `v*.*.*`

### Descriptor Version

The workflows overwrite the `version` field in both `rtw-style-loading-screens.mod` and `rtw-style-loading-screens/descriptor.mod` with the tag, minus its leading `v`:
- Tag `v1.0.1` → `version="1.0.1"`

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
| Steam Workshop Deploy | Automated (tag push or dispatch) | Requires Steam secrets |
| Nexus Mods Deploy | Automated (tag push or dispatch) | Requires `NEXUSMODS_API_KEY` |
| GitHub Release | Automated (release publication) | Attaches the release ZIP |
| Automated Tests | None | Visual verification only |

## Known Limitations

- No automated DDS validation in CI
- No automated resolution check
- No automated descriptor validation
- Nexus Mods upload not automated
- No staging environment (direct to production channels)
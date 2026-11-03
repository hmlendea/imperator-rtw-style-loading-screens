# Change Guide

## Overview

This guide covers safe modification patterns for common areas of the mod. Since this is a static asset mod with no code, "changes" mean asset replacement, descriptor updates, and release management.

## Common Change Scenarios

### Replacing a Loading Screen

**Scope**: Single asset file (`load_N.dds`)

**Steps**:
1. Create new artwork at 3840×2160 in image editor
2. Export as DDS (BC3/DXT5 compression)
3. Replace `rtw-style-loading-screens/gfx/loadingscreens/load_N.dds`
4. Test in-game (copy to game mod dir, launch, verify)
5. Commit with message: `Replace load_N.dds: <description>`
6. PR → review → merge → maintainer tags release

**Validation**:
- File opens in GIMP/paint.net
- Resolution 3840×2160
- BC3/DXT5 format
- No visual artefacts in-game

**Risk**: Low. Single file, no dependencies.

### Replacing All Loading Screens (Full Redesign)

**Scope**: All 10 asset files

**Steps**:
1. Prepare all 10 new DDS files
2. Replace all `load_0.dds` … `load_9.dds` in single commit
3. Update `descriptor.mod` version (minor or major)
4. Test all 10 screens in-game
5. Commit: `Redesign all loading screens: <theme>`
6. PR → review → merge → maintainer tags release

**Validation**: Full manual verification checklist (see [Testing](./testing.md))

**Risk**: Medium. All assets changed; higher chance of one corrupt file.

### Updating Game Compatibility (New Game Version)

**Scope**: `descriptor.mod` `supported_version` field

**Trigger**: Imperator: Rome major version update (e.g., 2.0 → 3.0)

**Steps**:
1. Update `supported_version` in `descriptor.mod` (e.g., `3.0.*`)
2. Test mod with new game version (full verification)
3. Update `version` field (major bump recommended)
4. Commit: `Update supported_version to 3.0.*`
4. Tag release: `git tag v2.0.0 && git push --tags`

**Validation**: Full in-game test on new game version

**Risk**: High if game mod API changed. May require asset re-export.

### Updating Thumbnail

**Scope**: `thumbnail.png`

**Steps**:
1. Create new 1280×720 PNG
2. Replace `rtw-style-loading-screens/thumbnail.png`
3. Test: launcher shows new thumbnail
4. Commit: `Update thumbnail`
5. Include in next release

**Risk**: Very low. No functional impact.

### Updating Descriptor Version Only

**Scope**: `descriptor.mod` `version` field

**When**: Bugfix release, metadata-only change

**Steps**:
1. Update `version="vX.Y.Z"`
2. Commit: `Bump version to vX.Y.Z`
3. Tag release

**Risk**: None.

## Release Management

### Versioning Scheme

| Change Type | Version Bump | Example |
|-------------|--------------|---------|
| Asset replacement (1-2 screens) | PATCH | 1.0.0 → 1.0.1 |
| Full redesign / many screens | MINOR | 1.0.1 → 1.1.0 |
| Game major version support | MAJOR | 1.1.0 → 2.0.0 |
| Metadata only | PATCH | 1.0.0 → 1.0.1 |

### Tagging Procedure

```bash
# 1. Ensure main branch up to date
git checkout main && git pull

# 2. Verify all changes committed
git status

# 3. Create annotated tag
git tag -a v1.0.1 -m "Release v1.0.1: Replace load_3.dds"

# 4. Push tag (triggers GitHub Actions)
git push origin v1.0.1

# 5. Verify Actions workflow completes
# https://github.com/hmlendea/imperator-rtw-style-loading-screens/actions

# 6. Manually upload same ZIP to Nexus Mods
```

### Rollback Procedure

```bash
# If release has critical issue:
# 1. Delete tag locally and remotely
git tag -d v1.0.1
git push origin :refs/tags/v1.0.1

# 2. Fix issue on main branch
# 3. Re-tag with next version
git tag v1.0.2
git push --tags
```

## Safe Practices

### Do

- Test every asset change in-game before committing
- Keep PRs focused (one screen or one logical change)
- Use descriptive commit messages
- Update `descriptor.mod` version appropriately
- Verify file count (10 DDS) after changes

### Don't

- Rename `load_N.dds` files
- Add/remove loading screen files
- Change `descriptor.mod` `name` or `path` fields
- Change Workshop `itemid` in workflow
- Push tags without testing
- Commit untested DDS files

## File Modification Impact Matrix

| File | Safe to Modify? | Requires Release? | Testing Required |
|------|-----------------|-------------------|------------------|
| `load_N.dds` | Yes | Yes | Full visual |
| `descriptor.mod` version | Yes | Yes | Launcher check |
| `descriptor.mod` supported_version | Yes | Yes | Full game test |
| `descriptor.mod` name/path/tags | **No** | N/A | Breaks mod |
| `thumbnail.png` | Yes | Yes | Launcher check |
| `.github/workflows/steam-workshop.yml` | Rarely | No | Actions test |
| Root docs (README, etc.) | Yes | No | Visual |

## Emergency Procedures

### Corrupt Asset Shipped

1. Fix asset locally
2. Commit fix
3. Tag patch release immediately (PATCH version)
4. Workshop auto-updates subscribers

### Wrong supported_version

1. Correct `descriptor.mod`
2. Tag patch release
3. Workshop auto-updates

### Workshop Publish Failed

1. Check GitHub Actions logs
2. Fix workflow / rotate secrets
3. Re-run workflow or manual upload
4. If tag already pushed: delete and re-tag (see Rollback)

## Checklist for Every Change

- [ ] Asset(s) valid DDS, 4K, BC3/DXT5
- [ ] All 10 loading screens present
- [ ] `descriptor.mod` version updated appropriately
- [ ] `descriptor.mod` `supported_version` correct
- [ ] In-game test passed (all screens, no vanilla fallback)
- [ ] Launcher shows correct version and thumbnail
- [ ] Commit messages descriptive
- [ ] PR description clear
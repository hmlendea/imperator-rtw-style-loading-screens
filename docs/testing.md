# Testing

## Test Strategy

**No automated test suite exists.** This is a static asset mod with no executable code. Verification is entirely manual and visual.

## Test Organisation

### Manual Verification Checklist (Per Release)

| Test Case | Steps | Expected Result | Pass/Fail |
|-----------|-------|-----------------|-----------|
| **Mod Loads** | 1. Install mod<br>2. Launch game<br>3. Check launcher mod list | Mod appears with correct name, version, thumbnail | |
| **Loading Screen 0** | Start new game / load save | `load_0.dds` displays (RTW art, no artefacts) | |
| **Loading Screen 1** | Repeat until screen 1 appears | `load_1.dds` displays correctly | |
| **Loading Screen 2** | Repeat until screen 2 appears | `load_2.dds` displays correctly | |
| **Loading Screen 3** | Repeat until screen 3 appears | `load_3.dds` displays correctly | |
| **Loading Screen 4** | Repeat until screen 4 appears | `load_4.dds` displays correctly | |
| **Loading Screen 5** | Repeat until screen 5 appears | `load_5.dds` displays correctly | |
| **Loading Screen 6** | Repeat until screen 6 appears | `load_6.dds` displays correctly | |
| **Loading Screen 7** | Repeat until screen 7 appears | `load_7.dds` displays correctly | |
| **Loading Screen 8** | Repeat until screen 8 appears | `load_8.dds` displays correctly | |
| **Loading Screen 9** | Repeat until screen 9 appears | `load_9.dds` displays correctly | |
| **No Vanilla Fallback** | Observe multiple loads | Never see vanilla loading screens | |
| **Aspect Ratio** | Check on 16:9, 16:10, 21:9 | Correct scaling, no stretching | |
| **Workshop Install** | Subscribe via Workshop, launch | Mod auto-installs, works identically | |
| **Manual Install** | Extract ZIP to mod dir, launch | Mod loads, works identically | |

### Asset Validation (Pre-Commit)

| Check | Method | Tool |
|-------|--------|------|
| File exists | `ls gfx/loadingscreens/load_*.dds` | Shell |
| Valid DDS | Open in image viewer (GIMP, paint.net) | Manual |
| 4K resolution | Image properties (3840×2160) | Manual |
| BC3/DXT5 compression | Image properties / export settings | Manual |
| No corruption | Full render in viewer | Manual |

## Coverage

| Area | Coverage | Method |
|------|----------|--------|
| Asset presence | 100% (10/10 files) | File count check |
| Asset validity | 100% (manual) | Visual inspection |
| Game integration | 100% (manual) | In-game verification |
| Distribution | 100% (manual) | Workshop/Nexus/GitHub install test |
| Compatibility | Single version (2.0.*) | Targeted testing |

## Test Environments

| Environment | Game Version | OS | Distribution |
|-------------|--------------|-----|--------------|
| Primary | Latest 2.0.x | Windows 10/11 | Steam Workshop |
| Secondary | Latest 2.0.x | Linux (Proton) | Manual / Workshop |
| Tertiary | Latest 2.0.x | macOS | Manual |

## Regression Testing

**Trigger**: Any asset replacement or descriptor change.

**Scope**: Full manual verification checklist.

**Frequency**: Per release (tag push).

## Known Gaps

| Gap | Risk | Mitigation |
|-----|------|------------|
| No automated DDS validation | Corrupt file ships | Maintainer visual check |
| No resolution enforcement | Wrong resolution ships | Export pipeline discipline |
| No descriptor validation | Syntax error disables mod | Manual descriptor review |
| Single game version tested | 2.0.x patch regression | Test on latest patch |
| No multi-mod conflict test | Load order issues | Document as limitation |

## Future Test Improvements

| Improvement | Effort | Value |
|-------------|--------|-------|
| DDS header validation script | Low | Catch corrupt files |
| Resolution check script | Low | Enforce 4K |
| Descriptor syntax linter | Low | Catch typos |
| Automated Workshop install test | High | Verify distribution |
| Visual diff against approved | Medium | Regression detection |

## Test Execution

### Local Verification

```bash
# 1. Verify file set
ls -1 rtw-style-loading-screens/gfx/loadingscreens/load_*.dds | wc -l
# Expected: 10

# 2. Verify descriptor
cat rtw-style-loading-screens/descriptor.mod

# 3. Package for manual test
cd rtw-style-loading-screens && zip -r ../test-mod.zip . && cd ..

# 4. Install in game mod directory and launch game
# Manual steps follow
```

### CI Integration

**None currently.** GitHub Actions only packages and publishes; no validation steps.

## Test Data

**No test data required.** The mod itself is the test subject. Game provides loading screen triggers.

## Test Reporting

**Informal.** Maintainer verifies locally before tag push. No test reports, no dashboards, no CI integration.

## Debugging Failed Verification

| Symptom | Likely Cause | Investigation |
|---------|--------------|---------------|
| Mod not in launcher | Descriptor parse error | Check `descriptor.mod` syntax |
| Mod marked incompatible | `supported_version` mismatch | Verify game version vs descriptor |
| Purple/missing texture | Corrupt DDS or wrong format | Re-export DDS |
| Stretched image | Wrong resolution | Verify 3840×2160 |
| Vanilla screen appears | Missing mod file / load order | Check file count, other mods |
| Workshop not updating | Tag not pushed / workflow failed | Check Actions log |
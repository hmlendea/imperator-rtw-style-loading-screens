# Invariants

## System-Wide Invariants

System-wide invariants are properties that must remain true across all operations, releases, and deployment environments.

### 1. Asset Parity Invariant

**Invariant**: The mod must contain exactly ten loading screen files named `load_0.dds` through `load_9.dds`.

**Rationale**: Imperator: Rome selects loading screens from this fixed set by index (0-9). Fewer files → vanilla fallback. More files → ignored.

**Enforcement**: Pre-release checklist; manual file count (`ls | wc -l`).

**Consequence of Breach**: Mixed vanilla/mod screens; broken user experience.

### 2. Format and Resolution Invariant

**Invariant**: All loading screen assets must be in DDS format (BC3/DXT5 compression) at 3840×2160 (4K) resolution.

**Rationale**: Game texture loader requires DDS. 4K is project specification for high-dpi displays.

**Enforcement**: Artist export pipeline; image editor validation.

**Consequence of Breach**: Purple texture (corrupt/wrong format) or stretched display (wrong resolution).

### 3. Descriptor Configuration Invariant

**Invariant**: `descriptor.mod` must contain `name="rtw-style-loading-screens"`, `path="mod/rtw-style-loading-screens"`, and `supported_version="2.0.*"`.

**Rationale**: Required for game mod loader to mount assets correctly and declare compatibility.

**Enforcement**: Pre-release descriptor review.

**Consequence of Breach**: Mod disabled in launcher or fails to mount.

### 4. Zero-Runtime Invariant

**Invariant**: The mod must contain no executable code, runtime scripts, configuration files, or external network requests.

**Rationale**: Design goal — pure content replacement; maximum compatibility, zero security risk.

**Enforcement**: Directory layout restriction; repo scope.

**Consequence of Breach**: Violates security/privacy promise; potential instability.

### 5. Distribution ID Stability Invariant

**Invariant**: Steam Workshop Item ID (`3513150774`) and Nexus Mods ID (`25`) must remain immutable across all releases.

**Rationale**: Changing IDs would orphan all existing subscribers/downloaders.

**Enforcement**: Hardcoded in workflow and docs; maintainer policy.

**Consequence of Breach**: Loss of subscriber base; fragmented distribution.

### 6. License Integrity Invariant

**Invariant**: All assets and documentation must remain licensed under GNU General Public License v3.0.

**Rationale**: Copyleft requirement; open-source community standards.

**Enforcement**: `LICENSE` file at root; license header in docs.

**Consequence of Breach**: Legal non-compliance; license violation.

## Component-Level Invariants

### Mod Descriptor (`descriptor.mod`)

- File location fixed at `rtw-style-loading-screens/descriptor.mod`
- Clausewitz syntax valid
- `version` string matches git tag (without `v`)
- `tags` contains `"Interface"`

### Loading Screen Directory (`gfx/loadingscreens/`)

- Directory path fixed at `rtw-style-loading-screens/gfx/loadingscreens/`
- No subdirectories
- No non-DDS files
- Exactly 10 files

### Thumbnail (`thumbnail.png`)

- File location fixed at `rtw-style-loading-screens/thumbnail.png`
- PNG format
- Resolution suitable for launcher (~1280×720)

### Distribution Pipeline (`.github/workflows/steam-workshop.yml`)

- Triggered on tag `v*.*.*`
- Uploads ZIP containing `rtw-style-loading-screens/`
- Uses correct secrets (`STEAM_USERNAME`, `STEAM_PASSWORD`, `STEAM_2FASEED`)

## Invariant Violation Handling

| Invariant | Detection | Action |
|-----------|-----------|--------|
| Asset Parity | `ls \| wc -l` != 10 | Block release; add/remove files |
| Format/Resolution | Visual error in-game | Re-export DDS; test |
| Descriptor Config | Mod fails to load | Fix descriptor; re-test |
| Zero-Runtime | Non-asset file present | Remove file; review PR |
| ID Stability | Code review | Reject workflow change |
| License | License check | Restore GPL v3.0 |

## Verification Matrix

| Invariant | Pre-Commit | Pre-Release | Post-Deploy |
|-----------|------------|-------------|-------------|
| Asset Parity | Manual | Checklist | In-game |
| Format/Resolution | Editor | Visual | In-game |
| Descriptor Config | Code review | Checklist | Launcher |
| Zero-Runtime | File tree | File tree | N/A |
| ID Stability | Code review | Workflow check | Workshop |
| License | File check | Repo audit | Releases |
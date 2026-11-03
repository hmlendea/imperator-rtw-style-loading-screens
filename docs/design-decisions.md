# Design Decisions

## Architectural Decisions

### ADR-001: Static Asset Replacement Only

**Status**: Accepted

**Context**: The mod's purpose is to replace vanilla loading screens with RTW-style artwork.

**Decision**: Implement as pure content mod with no executable code, scripting, or configuration.

**Consequences**:
- ✅ Zero runtime overhead
- ✅ Maximum compatibility (uses native mod loader)
- ✅ Simple maintenance (replace files only)
- ❌ No conditional loading, weighting, or randomisation
- ❌ Cannot add screens beyond vanilla count (10)
- ❌ No player configuration options

**Alternatives Considered**:
- Scripted mod with Lua/C#: Rejected — overkill, adds complexity, requires game scripting API
- Configuration file for screen selection: Rejected — game mod API doesn't support it

### ADR-002: Fixed Filename Set (`load_0.dds` … `load_9.dds`)

**Status**: Accepted

**Context**: Game engine selects loading screens by fixed index.

**Decision**: Match vanilla filename convention exactly; no dynamic discovery.

**Consequences**:
- ✅ Guaranteed compatibility with game loader
- ✅ Predictable behaviour
- ❌ Cannot add screens without game update
- ❌ Cannot rename or reorganise

### ADR-003: DDS Format with BC3/DXT5 Compression

**Status**: Accepted

**Context**: Game texture loader only accepts DDS for loading screens.

**Decision**: Export all assets as DDS (BC3/DXT5) at 4K resolution.

**Consequences**:
- ✅ Native format, optimal GPU upload
- ✅ Alpha channel for UI overlays
- ❌ Requires DDS-capable editor (GIMP/paint.net plugins)
- ❌ Not viewable in standard image viewers without plugins

**Alternatives Considered**:
- PNG/JPG with runtime conversion: Rejected — game doesn't support it
- Uncompressed DDS: Rejected — larger file size, no quality benefit

### ADR-004: 4K Resolution (3840×2160)

**Status**: Accepted

**Context**: Modern displays; project specification.

**Decision**: Author all assets at 4K; game downscales for lower resolutions.

**Consequences**:
- ✅ Crisp on 4K displays
- ✅ Future-proof
- ❌ Larger file sizes (~5-10 MB per DDS)
- ❌ Longer GPU upload on lower-end hardware

### ADR-005: Steam Workshop as Primary Distribution

**Status**: Accepted

**Context**: Imperator: Rome primarily on Steam; Workshop integration native.

**Decision**: Automate Steam Workshop publishing via GitHub Actions; manual Nexus/GitHub.

**Consequences**:
- ✅ Seamless for Steam players (auto-update)
- ✅ Single source of truth (Git tags)
- ❌ Nexus/GitHub require manual steps
- ❌ Workshop item ID immutable (cannot migrate)

### ADR-006: Git Tags as Release Triggers (`v*.*.*`)

**Status**: Accepted

**Context**: Need automated release pipeline.

**Decision**: Semantic version tags trigger GitHub Actions → Workshop upload.

**Consequences**:
- ✅ Clear version history
- ✅ Automated packaging
- ✅ Immutable releases
- ❌ Tag push = immediate production deploy (no staging)

### ADR-007: No Automated Testing

**Status**: Accepted

**Context**: Visual content mod; no code to test.

**Decision**: Manual visual verification only.

**Consequences**:
- ✅ No CI complexity
- ✅ Appropriate for asset-only project
- ❌ No regression detection for corrupt DDS
- ❌ No automated resolution/format validation

**Alternatives Considered**:
- DDS validation script: Deferred — low ROI for 10 files per release
- Visual regression testing: Rejected — overkill

## Technical Decisions

### TD-001: Clausewitz Script for Descriptor

**Rationale**: Required by Imperator: Rome mod loader. No alternative format supported.

### TD-002: Single Mod Directory Structure

**Rationale**: Game expects `descriptor.mod` at root with `path="mod/<name>"` mounting `gfx/`. Flat structure matches game expectations.

### TD-003: Thumbnail as PNG

**Rationale**: Launcher and Workshop accept PNG; better compression for UI thumbnail than DDS.

### TD-004: GPL v3.0 License

**Rationale**: Copyleft ensures derivative works remain open; aligns with Paradox modding community norms.

### TD-005: No Code of Conduct Enforcement Automation

**Rationale**: Small project; maintainer manual moderation sufficient.

## Art Direction Decisions

### ART-001: Rome: Total War (2004) Aesthetic

**Rationale**: Project goal; nostalgic appeal for RTW players.

**Constraints**:
- Painterly style
- Historical illustration
- Muted earth tones
- Roman military/civic themes
- No anachronistic elements

### ART-002: Ten Distinct Scenes

**Rationale**: Matches vanilla count; variety across loading sequences.

**Scenes** (inferred from asset set):
- Varied Roman contexts (battle, city, map, portrait, etc.)
- No duplicate compositions

## Rejected Alternatives

| Alternative | Reason |
|-------------|--------|
| Animated loading screens (video) | Game doesn't support |
| Weighted random selection | No mod API for metadata |
| Context-aware screens (war/peace) | No scripting hooks |
| Player-selectable screen pack | No configuration system |
| Higher resolution (8K) | Diminishing returns; file size |
| Lower resolution (1080p) | Blurry on 4K; against spec |
| WebP/AVIF format | Game loader DDS-only |
| Multiple mod variants (lite/full) | Only 10 files; no benefit |

## Decision Log Maintenance

Record new decisions in this file with:
- Unique ID (ADR/TD/ART-XXX)
- Status (Proposed/Accepted/Superseded)
- Context, Decision, Consequences
- Date and author
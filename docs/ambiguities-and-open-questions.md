# Ambiguities and Open Questions

## Unresolved Items

### 1. Game Version 3.0+ Compatibility

**Question**: Will the mod work with Imperator: Rome 3.0+ without changes?

**Status**: Unknown. Requires game update to test.

**Impact**: `descriptor.mod` `supported_version` may need update. Asset format may change.

**Resolution**: Test on 3.0 release; update descriptor and re-export if needed.

### 2. DDS Format Evolution

**Question**: Will Imperator: Rome change DDS format requirements (compression, mipmaps, colour space)?

**Status**: Unknown. Paradox may update rendering pipeline.

**Impact**: Current BC3/DXT5 4K assets may become invalid.

**Resolution**: Monitor game updates; test assets on each major version.

### 3. Loading Screen Count Change

**Question**: Will Paradox change the number of loading screens (currently 10)?

**Status**: Unknown. Game engine currently hardcoded to 10.

**Impact**: Adding/removing screens would require game update.

**Resolution**: Watch patch notes; adapt asset count if changed.

### 4. Mod API Enhancement

**Question**: Will Paradox add mod API for weighted/conditional loading screens?

**Status**: No indication. Current API is static replacement only.

**Impact**: Would enable advanced features (context-aware screens, weighting).

**Resolution**: Monitor modding community / Paradox announcements.

### 5. Nexus Mods Automation

**Question**: Can Nexus Mods upload be automated via API?

**Status**: Nexus has API but requires maintainer token; not yet implemented.

**Impact**: Manual step per release; risk of human error.

**Resolution**: Investigate Nexus API; implement if feasible.

### 6. Linux/macOS Native Testing

**Question**: Does the mod work correctly on Linux (Proton) and macOS?

**Status**: Assumed working (same assets, same game engine). Not natively tested.

**Impact**: Potential DDS driver differences on non-Windows.

**Resolution**: Test on Linux/Proton; document any issues.

### 7. Steam Workshop Changelog Format

**Question**: Is BBCode changenote the only supported format?

**Status**: Workshop API accepts BBCode. Markdown not supported.

**Impact**: Release notes must use BBCode tags.

**Resolution**: Current workflow uses BBCode; maintain.

### 8. Asset Source Format Preservation

**Question**: Are source files (PSD/KRA/XCF) preserved for future edits?

**Status**: Not in repository (binary, large). Only exported DDS committed.

**Impact**: Future edits require re-creation from scratch or external source backup.

**Resolution**: Document source file location in maintainer notes (external).

### 9. Thumbnail Resolution Standard

**Question**: Is 1280×720 the optimal thumbnail resolution?

**Status**: Works for launcher/Workshop. No official spec found.

**Impact**: Suboptimal display on high-DPI launchers.

**Resolution**: Test higher resolution (1920×1080); adopt if supported.

### 10. Mod Load Order Conflicts

**Question**: How does this mod interact with other loading screen mods?

**Status**: Last-loaded wins per file (standard VFS precedence). No coordination.

**Impact**: Players using multiple loading screen mods get unpredictable results.

**Resolution**: Document in FAQ; recommend single loading screen mod.

## Known Gaps

| Gap | Severity | Owner | Target Resolution |
|-----|----------|-------|-------------------|
| No automated DDS validation | Medium | Maintainer | Add CI check |
| No resolution enforcement | Medium | Maintainer | Add CI check |
| No descriptor syntax validation | Low | Maintainer | Add CI check |
| No multi-platform test matrix | Low | Maintainer | Document tested platforms |
| No source asset backup in repo | Low | Maintainer | External backup documented |
| Nexus upload not automated | Low | Maintainer | API integration |

## Decisions Deferred

| Decision | Reason | Revisit Trigger |
|----------|--------|-----------------|
| Automated DDS validation | Low ROI (10 files/release) | Corrupt asset ships |
| Higher resolution assets | File size vs quality tradeoff | 8K displays common |
| Multiple mod variants | No user demand | Community request |
| Animated loading screens | Game engine doesn't support | Paradox adds support |
| Configuration system | Mod API doesn't support | Paradox adds support |

## Tracking

Review this document:
- Per game major version release
- Per release if issues arise
- Quarterly for stale items
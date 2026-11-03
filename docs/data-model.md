# Data Model

## Domain Entities

This mod is a pure content replacement with no executable code, database, or persistent state. The "data model" consists entirely of static asset files and their metadata.

### Loading Screen Asset

| Property | Value | Description |
|----------|-------|-------------|
| **Entity** | `LoadingScreen` | Single replacement loading screen image |
| **Identifier** | `load_N` where N ∈ [0,9] | Fixed index matching game expectation |
| **File Format** | DDS (DirectDraw Surface) | BC3/DXT5 compression typical |
| **Resolution** | 3840×2160 (4K) | Project specification |
| **Colour Space** | sRGB | Standard for game textures |
| **Mipmap Levels** | 1 (typical) | Full-screen display, no minification |
| **Alpha Channel** | Present (BC3) | For potential UI overlays |
| **Art Style** | Rome: Total War (2004) aesthetic | Painterly, historical illustration |

### Mod Descriptor

| Property | Value | Description |
|----------|-------|-------------|
| **Entity** | `ModDescriptor` | Mod metadata and configuration |
| **File** | `descriptor.mod` | Clausewitz script format |
| **Fields** | `version`, `tags`, `name`, `supported_version`, `path` | See [Architecture](./architecture.md#mod-descriptor-descriptormod) |

### Thumbnail Asset

| Property | Value | Description |
|----------|-------|-------------|
| **Entity** | `ModThumbnail` | Launcher/Workshop display image |
| **File** | `thumbnail.png` | PNG format |
| **Resolution** | ~1280×720 (16:9) | Typical launcher thumbnail size |
| **Purpose** | Visual identification only | No runtime function |

## Relationships

```
ModDescriptor (1) ── mounts ──> (1) AssetDirectory
AssetDirectory (1) ── contains ──> (10) LoadingScreen
AssetDirectory (1) ── contains ──> (1) ModThumbnail
```

No other relationships exist. No foreign keys, no references, no navigation properties.

## Persistence

**None**. All entities are immutable files in the repository and deployed mod package. No database, no ORM, no migration, no cache.

## Serialisation

| Entity | Serialisation | Location |
|--------|---------------|----------|
| `LoadingScreen` | DDS binary | `gfx/loadingscreens/load_N.dds` |
| `ModDescriptor` | Clausewitz text | `descriptor.mod` |
| `ModThumbnail` | PNG binary | `thumbnail.png` |

## Versioning

- **Asset Version**: Implicit via git commit / release tag (`v*.*.*`)
- **Descriptor Version**: Explicit `version` field in `descriptor.mod`
- **Game Compatibility**: `supported_version` field (semantic version range)

## Invariants

1. **Cardinality**: Exactly 10 `LoadingScreen` entities (indices 0-9)
2. **Naming**: `load_N.dds` with sequential N, no gaps
3. **Format**: All `LoadingScreen` entities must be valid DDS
4. **Resolution**: All `LoadingScreen` entities at 4K (3840×2160)
5. **Descriptor Consistency**: `name` field matches directory name; `path` field is `mod/<name>`
6. **Supported Version**: `supported_version` matches target game major.minor

## Validation Rules

| Rule | Enforcement | Failure Consequence |
|------|-------------|---------------------|
| 10 loading screens present | Manual / CI check | Missing indices → vanilla fallback |
| Valid DDS format | Game texture loader | Corrupt → purple/missing texture |
| 4K resolution | Artist export pipeline | Wrong size → stretched display |
| Descriptor parses | Game mod loader | Parse error → mod disabled |
| Version compatible | Game mod loader | Mismatch → mod marked incompatible |

## Data Flow

```
Artist Source (PSD/KRA)
    → Export 4K DDS
    → Commit to repo
    → Tag release
    → GitHub Actions packages ZIP
    → Distribution channels
    → Player filesystem
    → Game VFS mount
    → GPU texture (runtime only)
```

No reverse flow. No user-generated content. No runtime modification.
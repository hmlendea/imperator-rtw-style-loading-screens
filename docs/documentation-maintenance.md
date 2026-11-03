# Documentation Maintenance

## Documentation Structure

```
docs/
├── api-reference/           # N/A (no API)
├── behaviour/               # N/A (no user behaviours beyond loading)
├── components/              # N/A (no code components)
├── flows/                   # N/A (no execution flows beyond asset load)
├── architecture.md          # Architecture complement
├── build-and-deployment.md  # Build/deploy pipeline
├── change-guide.md          # Safe modification patterns
├── configuration.md         # Configuration reference
├── data-model.md            # Static asset model
├── dependencies.md          # External/internal dependencies
├── design-decisions.md      # ADRs and rationale
├── documentation-maintenance.md  # This file
├── error-handling.md        # N/A (no runtime errors)
├── faq.md                   # Frequently asked questions
├── INDEX.md                 # Master index
├── invariants.md            # System invariants
├── integrations.md          # External integrations
├── logging.md               # N/A (no logging)
├── quick-start.md           # Getting started
├── repository-overview.md   # Repository purpose/scope
├── repository-structure.md  # Source tree layout
├── security.md              # Security complement
├── state-and-persistence.md # N/A (no state)
├── testing.md               # Test strategy
└── troubleshooting.md       # Common issues
```

## Maintenance Principles

### 1. Complement, Don't Duplicate Root Docs

Root documents (`ARCHITECTURE.md`, `README.md`, `SECURITY.md`, `PRIVACY.md`, `LICENSE`, `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`) are authoritative. `docs/` files:
- Elaborate on root content
- Provide implementation detail
- Add operational guidance
- **Never** reproduce root content verbatim

### 2. Single Source of Truth

| Information | Authoritative Source | Docs Reference |
|-------------|---------------------|----------------|
| Project purpose | `README.md` | `repository-overview.md` |
| Architecture | `ARCHITECTURE.md` | `architecture.md` |
| Security policy | `SECURITY.md` | `security.md` |
| Privacy | `PRIVACY.md` | (reference only) |
| License | `LICENSE` | (reference only) |
| Contributing | `CONTRIBUTING.md` | `quick-start.md` |
| Conduct | `CODE_OF_CONDUCT.md` | (reference only) |

### 3. Update Triggers

Update `docs/` when:

| Trigger | Files to Review |
|---------|-----------------|
| Asset format change | `data-model.md`, `architecture.md`, `configuration.md` |
| Game version support change | `architecture.md`, `dependencies.md`, `configuration.md`, `change-guide.md` |
| Distribution channel change | `build-and-deployment.md`, `dependencies.md`, `integrations.md` |
| New design decision | `design-decisions.md`, `architecture.md` |
| Release process change | `build-and-deployment.md`, `change-guide.md` |
| Troubleshooting pattern | `troubleshooting.md`, `faq.md` |
| Repository structure change | `repository-structure.md`, `repository-overview.md` |

### 4. Review Cadence

| Frequency | Action |
|-----------|--------|
| Per release | Verify `quick-start.md`, `change-guide.md`, `build-and-deployment.md` match current process |
| Per game major version | Full review of `architecture.md`, `dependencies.md`, `configuration.md`, `data-model.md` |
| Quarterly | Check links, update `INDEX.md` catalogue, verify root doc references |
| On contribution | Update relevant `docs/` file in same PR |

## Documentation Standards

### File Naming

- kebab-case: `repository-overview.md`
- Descriptive: `build-and-deployment.md` not `build.md`
- Consistent suffixes: `-guide.md`, `-reference.md`, `-model.md`

### Headings

- Sentence case: `## Repository purpose`
- Hierarchical: `#`, `##`, `###`
- No numbering in headings

### Cross-References

- Relative from `docs/` root: `[Architecture](./architecture.md)`
- Root docs: `[ARCHITECTURE.md](../ARCHITECTURE.md)`
- External: Full URLs

### Code Symbols

- Backticks with context: `` `descriptor.mod` ``, `` `load_N.dds` ``
- Paths relative to repo root: `rtw-style-loading-screens/gfx/loadingscreens/`

### Tables

- Markdown tables with header row
- Aligned columns for readability
- Units in headers: `Resolution (px)`, `Size (MB)`

## Maintenance Workflow

### Adding New Documentation

1. Identify appropriate file (existing or new)
2. Follow structure conventions
3. Cross-reference related files
4. Update `INDEX.md` catalogue
5. Link from relevant root doc if applicable

### Updating Existing Documentation

1. Locate file in `docs/`
2. Make minimal focused change
3. Verify cross-references still valid
4. Update `INDEX.md` if catalogue entry changes

### Deprecating Documentation

1. Move to `docs/archived/` (create if needed)
2. Update `INDEX.md` to note archived status
3. Add redirect note in archived file

## Quality Checks

### Pre-Commit Checklist

- [ ] No duplicate content from root docs
- [ ] All cross-references resolve
- [ ] Tables render correctly
- [ ] Code blocks have language hints
- [ ] Headings follow hierarchy
- [ ] File named per convention
- [ ] `INDEX.md` updated if new file

### Link Validation

```bash
# Check internal links (requires markdown-link-check or similar)
# Manual: click each link in rendered view
```

### Content Accuracy

- Verify against current repository state
- Test commands in `quick-start.md`, `change-guide.md`
- Confirm file paths exist
- Validate version numbers match

## Ownership

| Area | Owner | Review Required |
|------|-------|-----------------|
| `architecture.md` | Maintainer | Per game version |
| `build-and-deployment.md` | Maintainer | Per release |
| `change-guide.md` | Maintainer | Per process change |
| `configuration.md` | Maintainer | Per config change |
| `data-model.md` | Maintainer | Per asset format change |
| `dependencies.md` | Maintainer | Per dependency change |
| `design-decisions.md` | Maintainer | Per new ADR |
| `faq.md` | Maintainer | Per common question |
| `quick-start.md` | Maintainer | Per install change |
| `repository-overview.md` | Maintainer | Per scope change |
| `repository-structure.md` | Maintainer | Per structure change |
| `troubleshooting.md` | Maintainer | Per new issue pattern |
| `INDEX.md` | Maintainer | Per any docs change |

## Tools

| Task | Tool |
|------|------|
| Edit | VS Code / any Markdown editor |
| Preview | VS Code Markdown preview |
| Link check | `markdown-link-check` (optional) |
| Spell check | `cspell` (optional) |
| TOC generation | Manual (keep simple) |

## Archival Policy

- Keep all versions in git history
- No separate archive needed
- `docs/archived/` only for superseded files still referenced

## Metrics

Track (informally):
- Documentation coverage: % of repo areas documented
- Staleness: time since last update per file
- Accuracy: issues filed for incorrect docs
- Completeness: FAQ entries vs actual questions
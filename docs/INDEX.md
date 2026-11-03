# Documentation Index

**Repository**: imperator-rtw-style-loading-screens
**Last Reviewed**: 2025-01-15
**Owner**: Maintainer
**Coverage**: Complete (19/19 planned files)

---

## Repository Summary

This is a **content-only mod** for Imperator: Rome (game version 2.0.x) that replaces the 10 default loading screens with Rome: Total War-style artwork. The mod contains:

- **10 DDS assets** (4K, BC3/DXT5 compression) in `rtw-style-loading-screens/gfx/loadingscreens/`
- **1 descriptor.mod** (Clausewitz script) defining mod metadata
- **1 thumbnail.png** for launcher/Workshop display
- **1 GitHub Actions workflow** for Steam Workshop publishing
- **Root documentation** (README, ARCHITECTURE, SECURITY, PRIVACY, LICENSE, CODE_OF_CONDUCT, CONTRIBUTING)
- **This documentation set** (19 files under `docs/`)

No code, no runtime, no configuration, no database, no API, no logging.

---

## Root Document Map

| Document | Purpose | Link |
|----------|---------|------|
| README.md | Project overview, installation, links | [README.md](../README.md) |
| ARCHITECTURE.md | System architecture, data flow, deployment | [ARCHITECTURE.md](../ARCHITECTURE.md) |
| SECURITY.md | Vulnerability disclosure, threat model | [SECURITY.md](../SECURITY.md) |
| PRIVACY.md | Data handling (none) | [PRIVACY.md](../PRIVACY.md) |
| LICENSE | GNU GPL v3.0 | [LICENSE](../LICENSE) |
| CODE_OF_CONDUCT.md | Community standards | [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) |
| CONTRIBUTING.md | Contribution guidelines | [CONTRIBUTING.md](../CONTRIBUTING.md) |
| rtw-style-loading-screens.mod | Steam Workshop metadata | [rtw-style-loading-screens.mod](../rtw-style-loading-screens.mod) |
| .github/workflows/steam-workshop.yml | CI/CD pipeline | [steam-workshop.yml](../.github/workflows/steam-workshop.yml) |

---

## Documentation Catalogue (docs/)

### Core Understanding

| File | Description |
|------|-------------|
| [repository-overview.md](repository-overview.md) | High-level repository purpose, scope, and key facts |
| [repository-structure.md](repository-structure.md) | Directory tree, file inventory, naming conventions |
| [architecture.md](architecture.md) | System architecture, data flow, deployment model |
| [data-model.md](data-model.md) | Asset schema, descriptor fields, file formats |

### Build and Operations

| File | Description |
|------|-------------|
| [build-and-deployment.md](build-and-deployment.md) | Release process, packaging, Steam/Nexus/GitHub publishing |
| [configuration.md](configuration.md) | Mod configuration (descriptor only; no runtime config) |
| [dependencies.md](dependencies.md) | External dependencies (game, tools, platforms) |
| [testing.md](testing.md) | Validation procedures (visual, structural, integration) |

### Design and Decisions

| File | Description |
|------|-------------|
| [design-decisions.md](design-decisions.md) | Key architectural and asset decisions with rationale |
| [ambiguities-and-open-questions.md](ambiguities-and-open-questions.md) | Unresolved items, known gaps, deferred decisions |
| [state-and-persistence.md](state-and-persistence.md) | State model (none), persistence (none), asset lifecycle |

### Operations and Maintenance

| File | Description |
|------|-------------|
| [troubleshooting.md](troubleshooting.md) | Common issues, diagnosis, resolution steps |
| [faq.md](faq.md) | Frequently asked questions for users and maintainers |
| [quick-start.md](quick-start.md) | 5-minute onboarding for new contributors |
| [change-guide.md](change-guide.md) | How to modify assets, descriptor, workflow |
| [documentation-maintenance.md](documentation-maintenance.md) | Doc update triggers, review cadence, style guide |

### Cross-Cutting Concerns (Complement Root Docs)

| File | Description |
|------|-------------|
| [error-handling.md](error-handling.md) | Failure modes, graceful degradation, recovery |
| [invariants.md](invariants.md) | Must-hold properties across releases |
| [integrations.md](integrations.md) | External system interfaces (Steam, Nexus, GitHub, game) |
| [logging.md](logging.md) | Observability (none at runtime; CI logs only) |
| [security.md](security.md) | Implementation-specific security elaboration |

---

## Navigation Aids

### Start Here (New Contributor)

1. [quick-start.md](quick-start.md) — 5-minute setup
2. [repository-overview.md](repository-overview.md) — What this is
3. [repository-structure.md](repository-structure.md) — Where things are
4. [change-guide.md](change-guide.md) — How to make changes

### Deep Dive (Maintainer)

1. [architecture.md](architecture.md) — System model
2. [build-and-deployment.md](build-and-deployment.md) — Release process
3. [design-decisions.md](design-decisions.md) — Why decisions were made
4. [ambiguities-and-open-questions.md](ambiguities-and-open-questions.md) — What's uncertain
5. [invariants.md](invariants.md) — What must never break

### Flows (Process Reference)

| Flow | Document |
|------|----------|
| Create release | [build-and-deployment.md](build-and-deployment.md#release-process) |
| Update assets | [change-guide.md](change-guide.md#updating-loading-screen-assets) |
| Update descriptor | [change-guide.md](change-guide.md#updating-descriptormod) |
| Fix broken release | [troubleshooting.md](troubleshooting.md#release-failures) |
| Add platform | [integrations.md](integrations.md#adding-new-distribution-channel) |
| Update docs | [documentation-maintenance.md](documentation-maintenance.md#update-triggers) |

---

## Maintenance Metadata

| Field | Value |
|-------|-------|
| Last full review | 2025-01-15 |
| Next scheduled review | 2025-04-15 (quarterly) |
| Owner | Maintainer |
| Coverage status | Complete (19/19) |
| Stale files | None |
| Broken links | None (verified) |

### Review Checklist (Per Quarterly Review)

- [ ] All cross-references resolve
- [ ] No stale content (check game version, tool versions)
- [ ] Root document changes reflected
- [ ] New ambiguities captured
- [ ] Invariants still hold
- [ ] Quick-start still works

---

## File Creation Log

| File | Created | Purpose |
|------|---------|---------|
| repository-overview.md | 2025-01-15 | Core understanding |
| repository-structure.md | 2025-01-15 | Core understanding |
| architecture.md | 2025-01-15 | Core understanding |
| data-model.md | 2025-01-15 | Core understanding |
| build-and-deployment.md | 2025-01-15 | Build and operations |
| configuration.md | 2025-01-15 | Build and operations |
| dependencies.md | 2025-01-15 | Build and operations |
| testing.md | 2025-01-15 | Build and operations |
| design-decisions.md | 2025-01-15 | Design and decisions |
| ambiguities-and-open-questions.md | 2025-01-15 | Design and decisions |
| state-and-persistence.md | 2025-01-15 | Design and decisions |
| troubleshooting.md | 2025-01-15 | Operations and maintenance |
| faq.md | 2025-01-15 | Operations and maintenance |
| quick-start.md | 2025-01-15 | Operations and maintenance |
| change-guide.md | 2025-01-15 | Operations and maintenance |
| documentation-maintenance.md | 2025-01-15 | Operations and maintenance |
| error-handling.md | 2025-01-15 | Cross-cutting concerns |
| invariants.md | 2025-01-15 | Cross-cutting concerns |
| integrations.md | 2025-01-15 | Cross-cutting concerns |
| logging.md | 2025-01-15 | Cross-cutting concerns |
| security.md | 2025-01-15 | Cross-cutting concerns |

---

## Directory Structure (Reference)

```
docs/
├── INDEX.md                          # This file
├── repository-overview.md
├── repository-structure.md
├── architecture.md
├── data-model.md
├── build-and-deployment.md
├── configuration.md
├── dependencies.md
├── design-decisions.md
├── testing.md
├── troubleshooting.md
├── faq.md
├── quick-start.md
├── change-guide.md
├── documentation-maintenance.md
├── error-handling.md
├── invariants.md
├── integrations.md
├── logging.md
├── security.md
├── state-and-persistence.md
├── ambiguities-and-open-questions.md
├── api-reference/                    # Empty (not applicable)
├── behaviour/                        # Empty (not applicable)
├── components/                       # Empty (not applicable)
└── flows/                            # Empty (not applicable)
```

---

*Generated as part of comprehensive repository documentation. This index enables a future capable coding agent to navigate and understand the mod without rediscovering from source assets.*
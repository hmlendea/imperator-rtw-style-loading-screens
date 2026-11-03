# Security (Documentation Complement)

This document complements the root [SECURITY.md](../SECURITY.md) by elaborating on implementation-specific security considerations for this content-only mod.

## Threat Model

### Assets in Scope

| Asset | Sensitivity | Threat |
|-------|-------------|--------|
| DDS loading screens | Public (distributed) | Malicious replacement |
| Mod descriptor | Public | Metadata tampering |
| Thumbnail | Public | Visual spoofing |
| Distribution channels | Public | Supply chain compromise |

### Out of Scope

- Imperator: Rome game engine
- Steam/Valve infrastructure
- Nexus Mods infrastructure
- GitHub infrastructure
- Player local systems

## Security Properties

### Confidentiality

**No confidential data.** All mod assets are public by design. No secrets, tokens, or personal data in repository or distribution packages.

### Integrity

| Layer | Protection | Verification |
|-------|------------|--------------|
| Repository | Git signed commits | `git log --show-signature` |
| Release artefact | GitHub Release checksums | SHA256 on release page |
| Steam Workshop | Steam CDN + signature | Steam client verification |
| Nexus Mods | Nexus CDN | Nexus client verification |
| GitHub Releases | HTTPS + GitHub infrastructure | Browser verification |

### Availability

- Steam Workshop: Valve SLA
- Nexus Mods: Nexus SLA
- GitHub Releases: GitHub SLA
- No mod-specific availability requirements

## Vulnerability Categories

### In-Scope (Per SECURITY.md)

1. **Malicious DDS in Official Channels**
   - Vector: Compromised maintainer account / supply chain
   - Impact: Texture exploit in game renderer
   - Mitigation: PR review; signed commits; limited maintainer access

2. **Supply Chain Compromise**
   - Vector: GitHub Actions compromise / Steam credential theft
   - Impact: Malicious asset published to Workshop
   - Mitigation: Pinned Actions; secret rotation; 2FA

### Out-of-Scope

- Game engine vulnerabilities (Paradox responsibility)
- Mod loader vulnerabilities (Paradox responsibility)
- Steam/Valve platform vulnerabilities (Valve responsibility)
- Nexus Mods platform vulnerabilities (Nexus responsibility)
- Third-party repackages (not official channels)

## Secure Development Practices

### Repository

- Signed commits required for maintainers
- Branch protection on `main` (PR required)
- No direct pushes to `main`
- Dependabot not applicable (no dependencies)

### Release Process

- Tag-based triggers (immutable)
- GitHub Actions downloads from Release (verified artefact)
- Steam secrets in GitHub Secrets (not repo)
- Manual Nexus upload (human verification)

### Asset Validation

- Maintainer visual inspection of all DDS before release
- No automated DDS validation (future improvement)
- File count verification (10 files)

## Incident Response

### Malicious Asset Published

1. Revoke Steam secrets immediately
2. Delete Workshop version (if possible) or publish clean version
3. Rotate all credentials
4. Investigate source (compromised account vs supply chain)
5. Notify users via GitHub release notes / Workshop changenote

### Credential Compromise

1. Rotate affected secrets immediately
2. Audit recent workflow runs
3. Verify no malicious publishes occurred
4. Update secret storage

## Compliance

| Standard | Status | Notes |
|----------|--------|-------|
| GPL v3.0 | Compliant | License at root |
| No personal data | Compliant | See PRIVACY.md |
| Supply chain | Best effort | Pinned Actions; secrets |
| Vulnerability disclosure | SECURITY.md | Coordinated disclosure |

## Security Checklist (Per Release)

- [ ] All commits signed
- [ ] No new dependencies added
- [ ] DDS files visually inspected
- [ ] Descriptor unchanged (except version)
- [ ] Workshop workflow uses pinned Action version
- [ ] Secrets not in logs
- [ ] Release ZIP matches repo state
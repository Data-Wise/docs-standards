# docs-standards

**Shared documentation standards for Data-Wise projects**

---

## Quick Start

### MkDocs (Tools)

```yaml
# In your mkdocs.yml
INHERIT: https://raw.githubusercontent.com/Data-Wise/docs-standards/main/mkdocs-base.yml
site_name: Your Project
```

### R Packages (altdoc)

```r
altdoc::setup_docs("quarto_website")
# Copy quarto-base.yml and custom.scss to altdoc/
```

---

## Project Settings Contract & ADRs

Cross-tool decisions and the **born-ready project contract** live here:

- [`adr/ADR-001-research-ops-ecosystem-ownership.md`](adr/ADR-001-research-ops-ecosystem-ownership.md) — research-ops ownership map + Project Settings Contract.
- [`PROJECT-SETTINGS-CONTRACT.md`](PROJECT-SETTINGS-CONTRACT.md) — the checklist every project must satisfy (`.STATUS`, `.obs/sync.yml`, `CLAUDE.md`, atlas registration), **enforced by `atlas doctor`**.

## Files

| File | Purpose |
|------|---------|
| `mkdocs-base.yml` | MkDocs Material config |
| `quarto-base.yml` | Quarto website config |
| `custom.scss` | Shared theme (indigo/slate) |
| `DESIGN_STANDARDS.md` | Full style guide |
| `IMPLEMENTATION_PLAN.md` | Migration tracking |
| `DOCUMENTATION_INVENTORY.md` | Project status |

---

## Design

- **Primary:** Indigo `#6366f1`
- **Dark mode:** Built-in toggle
- **Top tabs:** Navigation
- **Code highlighting:** GitHub style

---

[View all Data-Wise projects](https://github.com/Data-Wise)

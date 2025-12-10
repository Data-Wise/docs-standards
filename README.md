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

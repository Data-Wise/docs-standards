# Data-Wise Design Standards

Unified design standards for all Data-Wise documentation.

---

## Color Palette

| Use | Color | Hex |
|-----|-------|-----|
| Primary | Indigo | `#6366f1` |
| Secondary | Slate | `#1e293b` |
| Accent | Purple | `#a855f7` |
| Success | Emerald | `#10b981` |
| Warning | Amber | `#f59e0b` |
| Danger | Red | `#ef4444` |

---

## MkDocs (Tools & Frameworks)

**Use for:** emacs-r-devkit, claude-r-dev, zsh-claude-workflow, examark

### Setup

```yaml
# In your mkdocs.yml
INHERIT: https://raw.githubusercontent.com/Data-Wise/docs-standards/main/mkdocs-base.yml

site_name: Your Project
site_url: https://data-wise.github.io/your-project/
repo_url: https://github.com/Data-Wise/your-project
```

### Required Files

- `mkdocs-base.yml` - Shared config (this repo)
- Project `mkdocs.yml` - Inherits base
- `docs/` folder with content

---

## altdoc + Quarto (R Packages)

**Use for:** mediationverse, medfit, probmed, rmediation, etc.

### Setup

```r
install.packages("altdoc")
altdoc::setup_docs("quarto_website")
altdoc::setup_github_actions()
```

### Required Files

- `quarto-base.yml` - Copy to `altdoc/_quarto.yml`
- `custom.scss` - Copy to `altdoc/`
- Replace `{{PACKAGE_NAME}}` in `_quarto.yml`

### Per-Package Checklist

- [ ] `altdoc::setup_docs("quarto_website")`
- [ ] Copy `quarto-base.yml` → `_quarto.yml`
- [ ] Copy `custom.scss`
- [ ] `altdoc::render_docs()`
- [ ] `altdoc::setup_github_actions()`
- [ ] Commit & push

---

## File Structure

```
docs-standards/
├── README.md
├── DESIGN_STANDARDS.md      # This file
├── mkdocs-base.yml          # MkDocs shared config
├── quarto-base.yml          # Quarto shared config
├── custom.scss              # Shared SCSS theme
├── IMPLEMENTATION_PLAN.md   # Migration tracking
└── DOCUMENTATION_INVENTORY.md
```

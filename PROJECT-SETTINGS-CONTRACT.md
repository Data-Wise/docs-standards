# Project Settings Contract

Every Data-Wise project should satisfy this contract at birth and keep satisfying it. It is **enforced by
`atlas doctor`** ([atlas](https://github.com/Data-Wise/atlas)) and the rationale is recorded in
[`adr/ADR-001-research-ops-ecosystem-ownership.md`](adr/ADR-001-research-ops-ecosystem-ownership.md).

## Required at project birth

| File / state | Purpose | Created by | Scope | `atlas doctor` |
|---|---|---|---|---|
| `.STATUS` | atlas registry header (`status`/`priority`/`progress`/`next`/`type`; research adds `kind`/`target`/`tasks`) | `atlas init --template` · `research-scaffold` | all | **error** if missing |
| `.obs/sync.yml` | vault mirror map (`mirror: none` for non-vault projects) | `obs link` | all | info (until `obs link`) |
| `CLAUDE.md` | project rules / Claude context | scaffolder template · `atlas doctor --fix` | all | **warn** if missing |
| `.claude/` | commands / settings | scaffolder | optional | info |
| atlas registration | appears in the registry | `research-scaffold` (`atlas project add`) | all | **error** if absent |
| git (`main` ← `feature/*`, remote) | version control | scaffolder | all | warn |

## Ownership (who owns what)

- **Registry + status data** → **atlas** (`.STATUS`, `project list`, `atlas doctor`).
- **Vault sync** (render + mirror) → **obs** / obsidian-cli-ops (`.obs/sync.yml`, `obs link`, `obs research board`).
- **This contract (the spec)** → **docs-standards**; **enforcement** → **`atlas doctor`**.
- **Daily workflow UX** → **flow-cli** (`work`, `dash`) over the shared atlas registry.

## Validate

```bash
atlas doctor                 # audit every project against this contract
atlas doctor --fix --write   # create missing CLAUDE.md
```

`atlas doctor` exits non-zero on a missing `.STATUS`, so it doubles as a CI / launchd drift guard.

Decisions of record: [`adr/ADR-001`](adr/ADR-001-research-ops-ecosystem-ownership.md).

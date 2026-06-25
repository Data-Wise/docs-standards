# ADR — Research-Ops Ecosystem Ownership & Project Settings Contract

> Parent: RFC-000. Date: 2026-06-25. Status: **all decisions resolved (D1–D6 + O1/O2).** Home: `dev-tools/docs-standards`.
> Purpose: stop re-deriving "who owns what." One map for atlas / obs / savant / scholar / flow-cli /
> apple-notes-sync, plus the contract every project must satisfy at birth and who enforces it.

---

## 1. Context — why this ADR
Coverage drift (only 2 of N projects have the obsidian mirror map; `.STATUS` 51/57; `CLAUDE.md` 43/57)
traces to a single root cause: **nothing creates these files at project birth, and nothing audits them
after.** Meanwhile four tools touch the vault / status surface with overlapping responsibilities. This
ADR fixes ownership at **creation** and **validation**, and records the consolidation direction.

---

## 2. Ecosystem ownership map

| Tool | Lang | Owns (responsibility) | Reads | Writes |
|------|------|-----------------------|-------|--------|
| **flow-cli** | ZSH | **Daily workflow UX** — `work` (start session, friction-free), `dash`/`today`/`week` (priorities + schedule), streaks/dopamine, `flow sync` | `.STATUS` (`## Schedule:`), `.flow/teach-config.yml` | `.flow/` session state |
| **atlas** | Node | **Registry + sessions engine** — projects registry, `.STATUS` sync, `project list`/`--json`, MCP, **`atlas doctor` (new)** | `.STATUS`, `projects.json` | `~/.atlas` registry |
| **obs** (obsidian-cli-ops) | ZSH+Py | **Vault authority** — render (`research board`) **+ mirror** (new); owns `.obs/sync.yml` schema + `obs link` | atlas JSON/MCP, `.obs/sync.yml`, vault | vault dashboards (marker-bounded, atomic) |
| **savant** | Node | **Research skills + scaffold** — `research-scaffold` births research projects; `plan:*` (recap) | vault, `.STATUS` | scaffolds repos (mirror creation delegated to obs) |
| **scholar** | Node | **Teaching scaffold/commands** (slides/assignment/preflight) | course dirs | teaching materials |
| **apple-notes-sync** | AppleScript | **`.STATUS` → Apple Notes** (separate surface, not the vault) | `.STATUS` | Apple Notes |

**Key couplings to clean up:** `.flow/` (flow-cli's dir) currently also holds savant's `obsidian-sync.yml`
→ relocate the mirror map to obs-owned `.obs/sync.yml`. `.STATUS` is the shared contract across
flow-cli + atlas + obs — keep it the single source of project truth.

**flow-cli ↔ atlas (the relationship):** flow-cli is the **ZSH front-end UX**; atlas is the **Node engine**.
They coexist over a shared `.STATUS`/`projects.json` (flow-cli runs standalone *or* uses atlas). They are
**not** competitors — flow-cli = "start working / what's next", atlas = "registry + state + MCP". (See Open O1.)

---

## 3. Locked / recommended decisions

| # | Decision | Choice | Status |
|---|----------|--------|--------|
| **D1** | Vault-sync owner | **obs** consolidates render + mirror (staged: render now, mirror fast-follow). apple-notes-sync stays separate; atlas data-only. | recommended (consistent w/ D3) |
| **D2** | Mirror-map location | **Per-project, obs-owned schema**, relocated `.flow/obsidian-sync.yml → .obs/sync.yml`. | recommended (consistent w/ D3) |
| **D3** | Who **creates** the map at birth | **obs primitive** (`obs link`/`obs init`); scaffolders (atlas init, savant research-scaffold, scholar) call it. | ✅ locked |
| **D4** | Who **validates** project settings | **`atlas doctor`** — one command audits coverage across all projects + flags gaps. | ✅ locked |
| **D5** | Scope (who needs a map) | **Every project** carries one; non-vault projects (packages/dev-tools) use `mirror: none` so the file exists but is a no-op. | ✅ locked |
| **D6** | Rules / local Claude files | **Include `CLAUDE.md` + `.claude/`** in the contract + doctor check. `AGENTS.md` optional (only 3/57 use it). | recommended |

---

## 4. The Project Settings Contract (born-ready)

Every project, at creation, must have — and `atlas doctor` audits — the following:

| Setting | Purpose | Creator (D3) | Required | Doctor check |
|---------|---------|--------------|----------|--------------|
| `.STATUS` | atlas registry header (+ `## Schedule:` for flow-cli) | `atlas init --template` / `research-scaffold` | all | error if missing |
| `.obs/sync.yml` | vault mirror map (`mirror: none` for non-vault) | **`obs link`** (called by scaffolder) | all | error if missing |
| `CLAUDE.md` | project rules / Claude context | scaffolder template | all | warn if missing |
| `.claude/` | commands/settings (optional) | scaffolder | optional | info |
| atlas registration | appears in registry | `research-scaffold` (`atlas project add`) | all | error if absent |
| git (`main`+`draft` or `dev`) | VCS + remote | scaffolder | all | warn |

**Born-ready flow:** scaffolder (atlas init / savant research-scaffold / scholar) → seeds `.STATUS` +
`CLAUDE.md` + `git` → calls `obs link` (stamps `.obs/sync.yml`) → `atlas project add`. One call chain,
every file accounted for. `atlas doctor` then guarantees it stays true.

---

## 5. Coverage today (baseline to close)
- `.STATUS`: **51/57** (6 gaps = non-projects/backups)
- `CLAUDE.md`: **43/57** · `.claude/`: 39/57 · `AGENTS.md`: 3/57
- `.obs` mirror map: **2/57** ← the big gap
- Umbrella rules: `~/.claude/CLAUDE.md` (global) + `~/projects/dev-tools/CLAUDE.md` (dev-tools) exist.

---

## 6. Resolved (2026-06-25)
- **O1 — flow-cli ↔ atlas → ONE registry.** flow-cli `dash`/`work` read atlas's registry
  (`atlas project list --json` / shared `projects.json`); flow-cli stays the ZSH UX *over* the atlas
  engine — no second source of truth. (Eliminates the drift that motivated this ADR.)
- **O2 — spec home → `dev-tools/docs-standards`; enforcer → `atlas doctor`.** The Project Settings
  Contract + this ADR live in docs-standards (cross-tool, tool-agnostic); `atlas doctor` reads + enforces.
  Not folded into savant `plan:config` (would make savant own everyone else's settings).

---

## 7. Consequences / next actions
- **Build:** `obs link` (create `.obs/sync.yml`) → wire scaffolders to call it → `atlas doctor` (audit the
  contract) → backfill existing projects (mirror map + missing `CLAUDE.md`).
- **Migrate:** move savant `plan:obsidian-sync` mirror logic to obs; relocate `.flow/obsidian-sync.yml`
  → `.obs/sync.yml` (keep a shim one release).
- **Sequencing:** slots into Phase-3 Epic C (rollout) — does **not** block the obs research board (Epic B).
- **Cheap now:** backfill `CLAUDE.md` + a `mirror: none` `.obs/sync.yml` to the real projects missing them.

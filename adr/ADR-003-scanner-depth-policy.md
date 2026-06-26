# ADR-003: Scanner depth & umbrella sub-repo policy

**Status:** Accepted
**Date:** 2026-06-26
**Deciders:** DT
**Parent:** [ADR-001](ADR-001-research-ops-ecosystem-ownership.md)
**Related:** atlas #41 (FW-28)

## Context

atlas's filesystem scanner (`FileSystemProjectRepository._scanRecursive`, `maxDepth = 3`) **stops at the first
project-dir** it finds — when a directory is itself a project (has `.STATUS` / `package.json` / etc.), the scanner
records it and does **not** descend into its children. So an *umbrella* that is itself a project (`mcp-servers`,
`claude-plugins`) **hides its child repos** from the registry. But some of those children are **first-class**
dev-tools (`craft`, `rforge`). The result was inconsistent tracking and children that re-orphan on every
`atlas sync --remove-orphans`.

## Decision

1. **Umbrella-only is the default** — a project-dir is a scan leaf. This is simple and stable.
2. **Opt-in child recursion** via a **`.atlas-scan-children`** marker file in the umbrella: when present, the
   scanner records the umbrella *and* descends into its children (still bounded by `maxDepth`).
3. **Interim for the two known first-class nested repos** (`craft`, `rforge`): either drop a
   `.atlas-scan-children` marker in their umbrella, or register them explicitly (and exclude from orphan removal),
   until/if they are promoted to a top-level directory.

## Options considered

| Option | Complexity | Behavior | Trade-off |
|--------|-----------|----------|-----------|
| **A. Umbrella-only (status quo)** | Low | children untracked | simple; first-class nested repos invisible |
| **B. Opt-in `.atlas-scan-children` marker** ✅ | Med | umbrella descends when marked | precise; only opted-in umbrellas recurse; one new convention |
| **C. Promote first-class repos to top-level** | Low (manual) | move/symlink `craft`,`rforge` | no scanner change; touches filesystem layout |

## Trade-off analysis

**B as the durable mechanism, C as the interim for the two known repos.** B avoids the orphan whack-a-mole
without making *every* umbrella recurse (which would re-introduce noise like the old `mcp-servers/*` sprawl). C
unblocks `craft`/`rforge` immediately without waiting on a layout change.

## Consequences

- **Easier:** monorepo-style umbrellas can deliberately expose their children; the default stays clean.
- **Harder/Revisit:** the marker is a new convention to document and teach; scaffolders may want to drop it
  automatically for known monorepos.
- Bounded by `maxDepth` (3), so a marked umbrella cannot recurse without limit.

## Action items

1. [x] `.atlas-scan-children` opt-in recursion in the scanner (atlas #41) + integration test.
2. [ ] Document the marker in atlas `CONFIGURATION.md` / `CLI-REFERENCE.md`.
3. [ ] Decide per-repo for `craft` / `rforge`: marker on the umbrella, or promote to top-level.
4. [ ] (optional) Have scaffolders drop the marker for known monorepo umbrellas.

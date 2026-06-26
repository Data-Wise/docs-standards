# ADR-002: Sync research-metadata ownership

**Status:** Accepted
**Date:** 2026-06-26
**Deciders:** DT
**Parent:** [ADR-001](ADR-001-research-ops-ecosystem-ownership.md) (research-ops ecosystem ownership)
**Related:** atlas #36 (the bug), #40 (FW-27), #30/FW-30 (id-scheme follow-up)

## Context

atlas has two sync paths over the same `.STATUS` surface:

- **plain `atlas sync`** (`SyncRegistryUseCase`) — packages-only registry refresh. Does **not** parse research keys.
- **`atlas sync --from-status`** (`SyncFromStatusUseCase` / `StatusFileParser`) — parses `kind` / `target` / `tasks`.

A plain sync used to *strip* the research metadata a from-status sync had set (atlas #36), silently emptying the
research registry on every routine sync. #37 fixed the destruction (plain sync now **preserves** existing research
metadata), but plain sync was still not the *authority* and gave **no signal** that a research project had been
left stale — a second, quieter footgun. We need a documented contract for which path owns research metadata and
how plain sync should behave toward research projects.

## Decision

1. **`--from-status` is the sole authority** for research metadata (`kind` / `target` / `tasks` / `priority`):
   it parses and updates them.
2. **Plain sync preserves, never strips** (#37) and now **warns** when it encounters research projects it did
   not refresh — naming them and printing the remedy (`atlas sync --from-status`).
3. Provide **`atlas sync --research`** as a discoverable alias that forwards to `--from-status` (defaulting its
   scan root to `~/projects/research`).
4. **Defer** making `--from-status` the *default* sync (option C below) until the scan is benchmarked on the full tree.

## Options considered

### A. Preserve-only (post-#37, no signal)
| Dimension | Assessment |
|-----------|------------|
| Complexity | Low |
| Safety | Safe (no strip) |
| Clarity | Poor — silently stale; two mental models |

### B. Warn on touch + `--research` alias  ✅ chosen
| Dimension | Assessment |
|-----------|------------|
| Complexity | Low–Med |
| Safety | Safe; data semantics unchanged |
| Clarity | Good — discoverable remedy, no behavior surprise |

### C. `--from-status` becomes the default
| Dimension | Assessment |
|-----------|------------|
| Complexity | Med |
| Safety | One mental model |
| Risk | Possible perf regression on large trees; bigger blast radius |

## Trade-off analysis

**B now, C later.** B removes the silent-staleness footgun without changing data semantics or risking a perf
regression mid-release-train. C is the cleaner long-term model (one sync path) but should follow a benchmark of
`--from-status` scan time on the full project tree.

## Consequences

- **Easier:** users get a clear signal + remedy; research data is safe across routine syncs.
- **Revisit:** the warning fires on every plain sync that touches research projects (an intended nudge); option C
  remains open pending a benchmark.
- **Known related bug — FW-30 (id scheme).** The two paths derive project **ids** differently: `--from-status`
  uses a name-slug id; plain sync uses the full path. When `--from-status` registers a project *before* any plain
  sync, plain sync's `findById` misses → a **duplicate** registry entry, and neither preserve nor the warning fires
  for that entry. Tracked separately. The warning/preserve are reliable for the common case (a plain sync registers
  the project first, which is how the existing real-world registry was built).

## Action items

1. [x] Plain sync preserves + warns (atlas #37, #40).
2. [x] `atlas sync --research` alias.
3. [x] This ADR.
4. [ ] **FW-30** — unify the id scheme (match existing by path before `findById`); eliminates the duplicate + makes
   preserve/warn fire regardless of which path registered first.
5. [ ] (later) Benchmark `--from-status` scan; reconsider option C (default).

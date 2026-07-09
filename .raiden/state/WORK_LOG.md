# Work Log

## 2026-07-08 — RAIDEN Instance install

- Fleet scan (RAIDEN-ops LOOP-0021) identified SW-X1 as an active,
  git-managed project with no RAIDEN Instance.
- Created GitHub remote `StarlightDaemon/SW-X1` (public); initial push was
  blocked by GitHub's private-email policy on the pre-existing commits.
- Rewrote the 4 pre-install commits to the `StarlightDaemon` fleet git
  identity (D-001) and pushed `main`.
- Installed RAIDEN Instance at Edict v1.0.0: `.raiden/writ/` populated from
  the six-file managed payload, commit-msg hook installed, root `AGENTS.md`
  startup bridge written, D-0038 gitignore block appended.

## 2026-07-09 — Edict v2.0.0 + state normalization

- Applied Edict update 1.0.1 → 2.0.0 via `raiden_updater.cli` (plan then
  apply, both clean): added `ROUTING_POLICY.md`; removed `MODEL_TIERS.md`
  (managed file absent from new package, flagged `managed_file_removal` warn,
  safe per baseline); updated `README.md`, `OPERATING_RULES.md`,
  `WORKSPACE_AUDIT_PROTOCOL.md`, `FORK_REVIEW_PROTOCOL.md`, `AGENTS.md`;
  `hooks/commit-msg` unchanged. Re-plan confirmed "Already up to date".
- Stamped `"state_schema_version": 2` into `.raiden/instance/metadata.json`.
- Routing overlay: removed `.raiden/local/MODEL_MAP.md` (tier-to-model
  mapping, superseded); added `.raiden/local/ROUTING.md` (rung-ladder model
  routing, R1-R4 plus offload pool).
- State normalization per the new Writ's Fact-Home Rule and Cross-Repo
  Citation Namespace (`.raiden/writ/OPERATING_RULES.md`):
  - Dangling ref fix: `LOOP-0021` citations in `CURRENT_STATE.md` (Summary
    and Provenance) rewritten as `Raiden-ops:LOOP-0021` — it is a
    Raiden-ops loop, not a local one.
  - Removed hand-written "installed ... at Edict v1.0.0" / "(Edict v1.0.0)"
    version-prose strings from `CURRENT_STATE.md` (two instances),
    `GOALS.md`, and `OPEN_LOOPS.md` Provenance sections — the fact is
    already the authoritative record above (2026-07-08 entry) and in
    `.raiden/instance/metadata.json`; no unique history was lost.
  - No hand-written "Last Updated:"/"Last Verified:" footer lines were
    found in any `.raiden/state/` file (checked `CURRENT_STATE.md`,
    `GOALS.md`, `OPEN_LOOPS.md`, `DECISIONS.md`, `WORK_LOG.md`); none to
    remove.
  - LOOP-001/LOOP-002 (BLTouch hardware, `OPEN_LOOPS.md`) left as-is —
    hardware-gated, out of scope for this pass.

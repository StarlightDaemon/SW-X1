# SW-X1

The maintenance record for one Artillery Sidewinder X1 (Gen 1 Rev 1) 3D printer being recommissioned with a BLTouch probe (the Waggster mod). It holds verified firmware source and binaries, a configuration comparison, TFT firmware, printed-part files, wiring docs, calibration baselines and upstream backups. It is a public GitHub repository (StarlightDaemon/SW-X1). It is not a general Sidewinder X1 project: the scope is this one physical printer.

## Verifying changes

There's no build or test suite. Changes are documentation, firmware configuration and files. Verify by reading the actual file contents and checking links:
- **Governing rule:** trust the hardware (`M115`, `M119`, `G29`) and actual file contents, never a filename, README or changelog. This ecosystem has repeatedly mislabelled capability (`docs/verification-log.md`).
- Firmware claims are checked against the build's `Configuration.h` and the verification log, not against release notes.

## Layout

- `README.md`: overview, headline finding and repository map
- `docs/GUIDE.md`: the ordered end-to-end build guide (start here for hardware work); `install-runbook.md`, `hardware-baseline.md` (canonical facts; don't re-derive), `calibration-baseline.md`, `verification-log.md`, `configuration-diff.md`, `decisions.md`, `open-questions.md`
- `firmware/`: one folder per build (`build--…` built here, `reference--…`, `running--…`, `target--…`); `NOTES.md`
- `tft-firmware/`, `printed-parts/`, `wiring/`, `calibration/`
- `archive/`: upstream bundles and `SOURCES.md` (backups; don't edit)
- `.raiden/state/`: the project records (see below)

## Key rules

- **Never talk to the printer.** Don't open its serial port, send G-code, flash firmware or write to its SD card or TFT. Hardware work is the owner's, done by hand following `docs/GUIDE.md`.
- The firmware to flash is `firmware/build--X1-BLT-2.1.2.1-waggster-fixed/` (or the prebuilt `reference--digant73-2.0.9.1-waggster-zmax/`). The dgtaheno `X1-BLT` build as shipped is **not** Waggster-compatible.
- Don't change upstream copies in `firmware/reference--…`, `archive/` or `tft-firmware/`; they're backups. Record new findings in `docs/verification-log.md` or `docs/decisions.md`.
- Main branch is `main`. Agent work goes on `<agent>/<task>` branches in their own worktrees. See Standing delegation v1 in AGENTS.md.
- No `Co-Authored-By` or other agent attribution lines in commits; the `commit-msg` hook enforces this. Never bypass it.
- Local audit reports in `.audits/` are gitignored; never commit them.
- Don't edit `.raiden/writ/` or `.raiden/instance/`. They're historical RAIDEN v2 text kept for the old tooling. Where they, `.raiden/README.md` or `.raiden/local/` disagree with this file, this file wins.

## Project records

This file and `.raiden/state/` are authoritative for this repo. Read in this order and stop when you have what you need:
1. `.raiden/state/CURRENT_STATE.md`: where the recommissioning stands.
2. The `## Active` section at the top of `.raiden/state/OPEN_LOOPS.md`: open, deferred and held items (statuses: `open`, `deferred`, `held: <who>`, `no-action`, `accepted`, `closed`).
3. Only when needed: `.raiden/state/GOALS.md` (milestones), `.raiden/state/DECISIONS.md`, `WORK_LOG.md`, and the `docs/` files above (evidence).

Current picture: nothing for agents to do. Both loops are held for the owner's hardware work: LOOP-001 (install, flash and verify the BLTouch, waiting on the sensor) and LOOP-002 (bed warp mapping and calibration, after LOOP-001). Work them one loop at a time.

Update `CURRENT_STATE.md` and `OPEN_LOOPS.md` when you finish or pause work. Keep one home per fact and link evidence; don't paste it in. Never renumber or reuse a loop or decision ID.

## Working in parallel

One agent per worktree. Before starting, read the Claims section of `../../HANDOFF.md` (the workspace folder two levels above the repo or worktree root, outside Git, so every worktree sees the same file) and `git worktree list`, then add your claim line there; mark it done when you finish. Separate tasks may run in parallel only when both claims are listed there and they don't touch the same files. The owner (or the agent they assign) merges branches and updates `.raiden/state/`. The hook and Git config are one-at-a-time work.

This repository is public: don't commit personal names, absolute local paths or details of the owner's private tooling.

## Standing delegation v1 (operator: StarlightDaemon)
Applies to every agent working in this repo: Claude Code, Codex, Gemini CLI.

Do without asking, then report once at the end of the task:
- Read, build, test, lint. Wait for CI yourself; don't hand back "pending".
- Create agent branches (claude/*, codex/*, gemini/*); commit, rebase,
  fast-forward and merge them. Create agent worktrees.
- Push agent branches (never force). Open and update PRs; enable auto-fix.
- Merge a PR into the default branch when all checks pass, no review
  requests changes, and the merge doesn't publish (see Publication surface).
- Delete agent branches, local and remote, once their commits are in the
  default branch or another pushed branch.
- Keep records current: HANDOFF, OPEN-LOOPS, CHANGELOG and any doc that
  describes branch or PR state.

Ask once, in one message listing everything pending:
- Tags, releases, leaving a -dev version, or any merge that publishes.
- Force push, or rewriting history that's already pushed.
- Any other deletion: worktrees, backup refs, files or branches an agent
  didn't create, commits that exist nowhere else.
- Committing, moving or discarding StarlightDaemon's uncommitted work.
- CI workflows, branch protection, repo settings, secrets, agent permission
  settings, and this section.
- Anything against live hosts, services or devices.

Stop and report: failing checks you can't fix within the task, conflicts in
files StarlightDaemon is editing, or anything this section doesn't cover.

### Publication surface
Merging to `main` publishes nothing: there are no workflows and no install or update path serves files from `main`. Releases go out by tag only.

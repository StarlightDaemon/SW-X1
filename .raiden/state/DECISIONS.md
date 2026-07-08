# Decisions

## D-001 — Re-author pre-install commits to the fleet git identity (2026-07-08)

**Decision:** SW-X1's 4 pre-RAIDEN commits (originally authored as
`[redacted identity]`) were rewritten to
`StarlightDaemon <23347919+StarlightDaemon@users.noreply.github.com>`, matching
every other Instance in the fleet.

**Why:** GitHub rejected the initial push under its private-email publication
protection, since the personal email wasn't set to public. The operator chose
the rewrite (over allowing the private email or leaving history mixed) to
match fleet convention. No content changed — author/committer identity only.

**Binding rule this creates:** commits in this repo going forward use the
`StarlightDaemon` git identity (now the repo-local `user.name`/`user.email`
default), consistent with the rest of the fleet.

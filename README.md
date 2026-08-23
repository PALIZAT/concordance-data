# Concordance data

The canonical game database behind [Concordance](https://github.com/PALIZAT/concordance),
kept here so the app can update it without shipping a new version.

A concordance is an index that gathers every variant of a thing under one entry.
That is what this is: Steam sells *CHRONO TRIGGER*, PlayStation calls its trophy
set `NPWR01249_00`, and a shelf somewhere holds *Dark Souls II: Scholar of the
First Sin*. All of them are entries pointing at one game, and this database says
which.

## Files

| File | What it is |
|---|---|
| `db/games.jsonl` | One canonical game per line: id, name, release date |
| `db/links.jsonl` | One store entry per line, and the game it belongs to |
| `canon-decisions.json` | Human corrections, applied on top of everything else |
| `index.json` | Version and file list, so the app can check cheaply |

One record per line, so correcting a single game shows up as a single changed
line rather than a rewritten file.

## Identity

A game's id is a readable slug fixed when the record is created —
`chrono-trigger-1995`, `resident-evil-4-2005`, `resident-evil-4-2023` — and
never changed again, not even when a name is corrected later, because
everything else points at it.

Version and platform are deliberately *not* part of identity. A canonical game
is the thing that survives editions and machines, so "which game" lives in
`games.jsonl` and "which edition, where" lives in `links.jsonl`.

## Where it came from

Seeded once from IGDB, and ours from then on: a game renamed, renumbered or
reclassified there does not move anything here. Later imports arrive as
proposals to be accepted, never as silent edits, and a human decision is never
overwritten by a machine.

## Corrections

Grouping decisions in `canon-decisions.json` are curated: they override both the
database and whatever the app works out on its own. If something is grouped
wrongly, that file is where it gets fixed — and the fix reaches everyone the
next time their app checks in.

## Licence

The data is factual — names, dates, and which entry is which game. It was
gathered with the IGDB API; check IGDB's terms before redistributing it further.

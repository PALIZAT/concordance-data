# Concordance data

A database of games, and of which store entries belong to which game.

Dark Souls II is sold on Steam twice — the original, and Scholar of the First
Sin — and a PS3 disc of it carries a trophy set under an id that means nothing
to either. Three entries, one game. This is what says so.

Every store counts its own way and none of them agree, so anything that reads
more than one of them has to decide what counts as the same game. That decision
is what lives here: 190,700 games, 175,589 store entries pointing at them, and
a list of corrections made by hand where no rule was good enough.

It is published rather than bundled, so that a wrong date or a bad grouping can
be fixed for everyone without anything being reinstalled.

## Files

| File | What it is |
|---|---|
| `db/games.jsonl` | One canonical game per line: id, name, release date |
| `db/links.jsonl` | One store entry per line, and the game it belongs to |
| `canon-decisions.json` | Human corrections, applied on top of everything else |
| `index.json` | Version and file list, so a reader can check cheaply |
| `catalog.json` | The two files above packed into the one an app downloads |

**[FORMAT.md](FORMAT.md) is the contract**: every field, what it means, and how
an application keeps itself up to date. Read that before writing anything that
consumes this.

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

Grouping decisions in `canon-decisions.json` are made by hand and override
everything else. Some questions have no rule that answers them: two games are
called Demon's Souls, and only a person can say which one a particular disc is.
If something is grouped wrongly, that file is where it gets fixed — and the fix
reaches every reader the next time it checks in.

## What reads it

[Concordance](https://github.com/PALIZAT/concordance), a game tracker for macOS
and Windows, which is what this was built for. Nothing stops anything else
from reading it — [FORMAT.md](FORMAT.md) is written for that.

## Licence

The data is factual — names, dates, and which entry is which game. It was
gathered with the IGDB API; check IGDB's terms before redistributing it further.

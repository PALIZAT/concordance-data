# The published format

What an application downloads, and what every field means. Written down
because the second build of Concordance is a rewrite in another language: if
this only existed inside the first one's source, the rules would be read off
Swift and written down again slightly differently, which is the thing the
shared database exists to prevent.

Base address:

    https://raw.githubusercontent.com/PALIZAT/concordance-data/main/

## Keeping up to date

1. Read `index.json` — a few hundred bytes.
2. If its `version` is not greater than the one already installed, stop. This
   is the normal case and costs almost nothing.
3. Otherwise download `catalog.json` **and** `canon-decisions.json`. Both or
   neither: a new catalogue paired with old decisions is worse than an old
   catalogue, because the decisions exist to correct the catalogue.
4. Write them to disk, record the new version, and use the downloaded copies
   from then on.

An application ships copies of both so a first run works before anything is
downloaded, and so it works with no network at all. The downloaded copy always
wins once it exists.

```json
{
  "version": 5,
  "generated": "2026-08-24",
  "files": { "catalog": "catalog.json", "decisions": "canon-decisions.json" },
  "counts": { "games": 190700, "links": 175589 }
}
```

`version` is the only field that decides anything. It goes up when the data
changes; if it does not go up, nobody downloads.

## `catalog.json`

Every canonical game, and the store entries belonging to each. Around 17 MB.

```json
{"i":"thief-the-dark-project-1998","n":"Thief: The Dark Project","d":"1998-11-30",
 "e":[["211600","Expanded Edition"],["4200960","Remaster"]]}
```

| Field | Meaning |
|---|---|
| `i` | the canonical game's id — permanent, never reused, never rewritten |
| `n` | its name, as the game is known rather than as a store spells it |
| `d` | earliest release anywhere, `YYYY-MM-DD`. Absent when unknown |
| `c` | artwork id. Absent today: there is no cover database yet |
| `e` | store entries, `[id]` or `[id, role]`. A missing role means Base |

The ids in `e` are Steam app ids. Nothing else appears there yet.

Roles: `Base`, `Remaster`, `Port`, `Expanded Edition`, `Remake`, `VR`,
`Bundle`. They describe the entry, never the game: Devil May Cry 4: Special
Edition is a row inside Devil May Cry 4, labelled `Expanded Edition`.

**A bundle appears under every game it contains**, each time labelled
`Bundle`. Devil May Cry HD Collection is not a game; it is three, so it is
listed as an entry of each. A store shelf still shows it as the single thing
the store sells — that is the store's own counting, and it is left alone.

**Dates belong to the game, not the store.** Chrono Trigger is 1995 even
though Steam started selling it in 2018. The earliest release anywhere wins,
whatever the region — which is why a few names arrive in Japanese, and why
`titles` below exists.

## `canon-decisions.json`

Decisions made by a person, applied on top of everything else. This is where a
mistake gets fixed, and the fix reaches everyone the next time their app
checks in.

Keys are store-prefixed — `steam:236430`, `psn:NPWR01249_00` — except where a
canonical game is named, and then the key is its id: `demons-souls-2009`.

| Section | Shape | Meaning |
|---|---|---|
| `merges` | list of groups | these entries are one game, under a given id and title |
| `separate` | list of key lists | these must never end up in the same game |
| `hidden` | list of keys | show nowhere at all |
| `storeOnly` | list of keys | keep on that store's own shelf, keep off the canonical shelf |
| `alsoCountsAs` | key → list of keys | this one purchase is also a copy of those games |
| `links` | key → game id | this store entry *is* this canonical game |
| `titles` | game id → name | correct a canonical name |

An absent section means an empty one. A reader must not fail because a section
it expected is missing.

Two of these carry the weight in practice:

`alsoCountsAs` — HITMAN World of Assassination is filed as Hitman 3, but the
purchase carries Hitman and Hitman 2 as well, so it counts as a copy of all
three.

`links` — PlayStation gives no id any catalogue shares, so a copy is matched
by name, and only when exactly one game answers to that name. Two games are
called Demon's Souls; a person says which one the PS3 disc is. Never guess:
matching by similarity was tried and paired ICO with a game called Tico.

## `db/games.jsonl` and `db/links.jsonl`

The database the catalogue is generated from — one record per line, so
correcting a single game shows up in git as a single changed line. An
application does not need these; they are here so the data can be read,
reviewed and corrected by people.

```json
{"id": "thief-ii-the-metal-age-2000", "name": "Thief II: The Metal Age", "igdb": 1, "released": "2000-03-21"}
{"key": "steam:211600", "game": "thief-the-dark-project-1998", "role": "Expanded Edition"}
```

A record may also carry `pinned` (fields an import must not touch), `retired`
(the date it disappeared from the source — kept, never deleted) and `added`.

## What is not in the data

Two rules live in the applications rather than here, because no catalogue
records them:

- Soundtracks, demos, servers, playtests, SDKs and the like are not games.
  They are recognised by their names, since no catalogue lists them at all.
- A game nobody has ruled on and no catalogue knows stands alone under the
  store's own title, with no release date rather than an invented one.

The golden test in the application repository pins down both, along with
everything above.

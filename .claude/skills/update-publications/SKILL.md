---
name: update-publications
description: Add new papers from Arthur Kosmala's Google Scholar to this site's publication list (_data/publications.json). Use when asked to "update publications", "add my new paper", "sync Scholar", or "refresh the publication list".
---

# Update the publication list

This site's publications are **curated by hand** in `_data/publications.json`. Author names,
venues, display order, and per-paper media are all maintained manually, so entries must be
added by hand — there is no automatic sync. Your job is to **add newly published papers while
preserving all existing curation**.

Scholar profile (reference only, for discovering new titles):
`https://scholar.google.com/citations?user=xJSuIP8AAAAJ&hl=en` (user id `xJSuIP8AAAAJ`).

## How an entry is rendered (read before editing)

`index.md` does:

```liquid
{% assign pubs = site.data.publications.items | sort: "year" | reverse %}
{% assign pub_groups = pubs | group_by: "year" %}
```

Consequences you must respect:

1. **Year grouping is automatic.** Papers are grouped and the groups shown newest-year first.
   You do not order years yourself; `sort: "year"` handles it.
2. **Within a year, display order = source order reversed.** `reverse` flips the whole array,
   so among papers of the *same* year, the one listed **last in the file** shows **first** on
   the page. Practical rule: **append a new paper to the END of the `items` array** and it will
   appear at (or near) the top of the page. That is the normal, correct place to add a new paper.
3. **Author highlighting** is done in `index.md` by replacing the exact string
   `Arthur Kosmala`. Always write his name exactly as `Arthur Kosmala` (this spelling) in the
   `authors` field so it gets bolded.

## Entry schema

Each item in `items` is a JSON object:

```json
{
  "title": "Exact Paper Title",
  "authors": "First Last and First Last and Arthur Kosmala and First Last",
  "venue": "Proceedings of <Conference> <Year>",
  "year": 2026,
  "citations": 0,
  "url": "https://arxiv.org/abs/XXXX.XXXXX"
}
```

Field rules:
- **authors**: full first + last names, separated by ` and ` (literal, with spaces). `index.md`
  splits on ` and ` and reformats to "A, B and C". Scholar shows initials like "A Kosmala, S
  Günnemann" — expand them to full names. If you are unsure of a co-author's full first name,
  keep their initial (e.g. `N. Kostin`) rather than inventing one, and flag it to the user.
- **title**: use the paper's true title (match arXiv / the proceedings, not a paraphrase).
- **venue**: prefer the published venue ("Proceedings of NeurIPS 2024", "Proceedings of ICML
  2026"); for workshop/preprint use the workshop name or "arXiv preprint arXiv:XXXX.XXXXX".
- **year**: integer. Use the publication year that should group it on the page.
- **citations**: integer (0 is fine; this is not currently displayed, the line is commented out).
- **url**: prefer the arXiv abstract page or the official proceedings page. If none exists,
  a Scholar citation link is acceptable: `https://scholar.google.com/citations?view_op=view_citation&hl=en&user=xJSuIP8AAAAJ`.

## Equal-contribution and corresponding-author marks

Author names may carry footnote symbols, appended directly to the name in the `authors`
string (the renderer treats them as part of the name):

- `*` = **equal contribution** (shared first / co-first authors), e.g.
  `Simon Geisler* and Arthur Kosmala*`.
- `†` = **shared corresponding author** (shared last author), e.g.
  `Meng Gao† and Brandon Wood†`.

A combined footnote at the end of the publications section in `index.md` defines them:
`* Equal contribution.  † Shared corresponding author.` (class `pub-footnote`, styled in
`site.css`). Keep that footnote whenever any `*` or `†` appears in the list.

**When adding a new entry, always ask the user:**
1. **Are there shared first (co-first / equal-contribution) authors?** If yes, append `*` to
   each of those author names.
2. **Are there shared last (shared corresponding) authors?** If yes, append `†` to each of
   those author names.

Additional rules:
- Get these marks from the **actual paper** (PDF / arXiv HTML footnotes), not from Scholar,
  which does not show them. If you cannot determine them, **ask the user** — do not guess
  authorship credit.
- Writing `Arthur Kosmala*` still triggers the name highlight: `index.md` replaces the
  substring `Arthur Kosmala`, leaving the `*` just after the bolded name. So always keep the
  spelling `Arthur Kosmala` intact and append the symbol after it.
- ⚠️ A `*` in a paper does not always mean equal contribution — e.g. a paper may use `*` to
  mean "work done during an internship". Only use `*` here for equal contribution and `†` for
  shared corresponding author; do not import a same-symbol footnote that means something else.
  When in doubt, ask the user how to annotate it.

## The media feature (do not break it)

The newest highlighted paper, **"Speculative Sampling For Faster Molecular Dynamics"**, has two
extra fields that render a GIF and an MP4 video player inside its tile:

```json
"media_gif": "/assets/overview.gif",
"media_video": "/assets/reflection_coupling.mp4"
```

- Keep these fields on that entry. The matching assets live in `assets/`.
- `index.md` renders `media_gif` first (as `<img>`), then `media_video` (as `<video controls>`),
  styled by the `.pub-media` rules in `assets/css/site.css`.
- **To give a different paper its own media:** put the asset(s) in `assets/` and add
  `media_gif` and/or `media_video` to that paper's entry.

## Workflow

1. **Get the current Scholar list.** Read the profile page
   (`https://scholar.google.com/citations?user=xJSuIP8AAAAJ&hl=en`) with WebFetch for reference.
   Note each title, author list, venue, and year.
2. **Diff against the curated file.** Compare Scholar titles to the `title` values already in
   `_data/publications.json`. Only **new** titles need adding. (Titles may differ in wording
   from Scholar — match on the actual paper, not exact string equality.)
3. **Add each new paper** as an entry following the schema above. **Append it to the end of the
   `items` array** so it surfaces at the top of its year group. Expand author initials; keep
   `Arthur Kosmala` spelled exactly. **Ask the user about shared first / last authors** and add
   the corresponding `*` / `†` marks.
4. **Validate JSON:**
   ```bash
   python3 -c "import json; d=json.load(open('_data/publications.json')); print('OK', len(d['items']), 'items')"
   ```
5. **Sanity-check ordering** by reasoning through "How an entry is rendered", or build the
   site if Jekyll is available (`bundle exec jekyll build`) and open `_site/index.html`.
6. Commit with a clear message (e.g. `Add <short title> to publications`). Do not push unless asked.

## Quick reference

- Data file: `_data/publications.json`
- Render logic: `index.md` (publications loop + author highlight + media block)
- Styling: `assets/css/site.css` (`.pub-*`, `.pub-media*`)
- Media assets: `assets/`
- Scholar user id: `xJSuIP8AAAAJ`
- Name to highlight: `Arthur Kosmala`

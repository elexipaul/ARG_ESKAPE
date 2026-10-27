---
name: literature-screening
description: Search scientific literature via Europe PMC and screen the results against explicit inclusion/exclusion criteria, reporting a PRISMA-style funnel (raw hits -> title/abstract-included -> full-text-included). Use for systematic literature search, screening papers for a review, deciding which papers to include/exclude, or triaging a large hit list down to a relevant working set. Not for one-off "find me a paper" lookups (use paper-lookup) and not for pulling numeric data fields out of full text into a database (not implemented here).
---

# Literature screening funnel

Adapted from the "Article retrieval and screening" methodology in Li et al.
2026, *npj Computational Materials* ("LLM-enabled automated data extraction
for concrete materials informatics"): broad database query -> retrieve full
text as structured XML (not PDF, which loses document structure) -> screen
against explicit criteria -> report a funnel of counts at each stage. This
skill implements the search-and-screen half only; screening decisions are
made by Claude reading the record against your criteria, not by a separate
LLM API call, so no API key is required.

Paths below are relative to `~/.claude/skills/literature-screening/`.

## Run this driver first (agent path)

`search_screen.py` talks to the Europe PMC REST API (`www.ebi.ac.uk/europepmc`),
which is free, key-less, and — like the paper's own pipeline — serves
open-access full text as structured JATS XML rather than PDF.

**1. Search and get funnel stage 1 (raw hits):**

```
python3 search_screen.py search --query "ESKAPE AND plasmid AND resistance" \
  --open-access --page-size 25 --limit 100 --out candidates.jsonl
```

Verified output (this session, real query):
```
# total hits: 2041
# fetched 100 of 2041 records
```
Each line of `candidates.jsonl` is one JSON record:
```json
{"pmid": "42698922", "pmcid": "PMC13543146", "doi": "10.4102/ajlm.v15i1.2893",
 "title": "Systematic review on genomic insights into antimicrobial resistance in ESKAPE pathogens.",
 "abstract": "<h4>Background</h4>Antimicrobial resistance (AMR) is a major global public health threat...",
 "journal": "African journal of laboratory medicine", "year": "2026",
 "isOpenAccess": "Y", "inEPMC": "Y"}
```

`--query` uses Europe PMC query syntax (field:value, AND/OR/NOT, quoted
phrases). `--open-access` appends `AND OPEN_ACCESS:Y` so every hit has
full text available via step 3. Drop `--limit` (or set to `0`) to page
through everything Europe PMC has — do this deliberately, hit counts can
be in the thousands.

**2. Screening protocol (the actual skill content):**

If the user hasn't already given inclusion/exclusion criteria, ask for them
(or state your inferred criteria and confirm) before screening — don't
invent criteria unilaterally. Then:

- Read `candidates.jsonl`. For each record, judge title + `abstract` against
  the criteria. `abstract` contains raw HTML tags (`<h4>`, `<i>`, `<b>` —
  Europe PMC returns structured-abstract markup as-is); read through them,
  don't be thrown off.
- Write a screening log, one row per record: pmcid, pmid, doi, title,
  decision (`include`/`exclude`), one-line reason. A simple CSV is enough —
  no fixed schema is enforced by this skill.
- Report funnel stage 2: `N raw hits -> M included after title/abstract screen`.

**3. Optional full-text pass, for criteria that need body-text detail:**

```
python3 search_screen.py fulltext --pmcid PMC13543146 --out fulltext/PMC13543146.xml
```

Verified output (this session, real PMCID):
```
# wrote 196221 bytes to fulltext/PMC13543146.xml
```
Produces JATS XML (`<article>` root, `<body>` with `<sec>`/`<table-wrap>`
elements) — parse it as XML, not as a blob of text, if you need specific
sections or tables. Only call this for records with `isOpenAccess: "Y"` from
step 1 (see Gotchas). Re-screen against criteria that the abstract alone
couldn't answer, then report funnel stage 3: `M -> K included after
full-text screen`.

**4. Deliverable:** hand back the final CSV of included papers plus the
funnel summary (e.g. "2041 hits -> 47 included on title/abstract -> 12
included on full text, criteria: ..."). This funnel + list is the output —
don't also try to extract data fields from the full text; that's a
separate, unbuilt skill.

## Gotchas

- **Journal title lives at `journalInfo.journal.title`**, not a top-level
  `journalTitle` field — the API's `core` result type nests it. The script
  already handles this; if you query the API directly, remember the nesting.
- **`fulltext` throws a raw `HTTPError: 500` with a Python traceback** for
  any PMCID that isn't open-access-available (wrong PMCID, or a real but
  closed-access record) — confirmed by testing `PMC99999999`. Check
  `isOpenAccess`/`inEPMC` from the search step before calling `fulltext`;
  don't loop over search results calling `fulltext` unconditionally.
- **Europe PMC pagination uses `nextCursorMark`**, not page numbers. The
  script follows it automatically; if you call the raw API yourself, start
  `cursorMark` at `*`.
- **`abstractText` contains embedded HTML tags** (structured abstracts use
  `<h4>Background</h4>...<h4>Methods</h4>...`) — this is normal, not a
  parsing bug.

## What this skill does not do

- Does not extract numeric/structured data fields from full text into a
  database (that's the extraction-agent half of the source paper, out of
  scope here — a possible future second skill).
- Does not call an external LLM API for screening — Claude does the
  screening directly, following the protocol above.
- Not for a single "find this one paper" lookup — use `paper-lookup` for that.

---
name: literature-helper-polsci
description: Use when mapping relevant literature, building annotated bibliographies, checking for gaps, or conducting systematic literature surveys for political science and computational social science research projects. Triggers on requests like "find the key literature on...", "am I missing major work?", "literature review for my project on...", or "build a bibliography for..."
---

# Literature Helper — Political Science

> **Version 1.0 | 2026-03-02**

Expert literature-review skill for political science and computational social science. Retrieves, verifies, screens, and synthesizes scholarship into a traceable, hyperlinked, analytically connected deliverable (`rough_literature_survey.md`).

The deliverable is not a list — it is a **narrative-driven survey** that maps, synthesizes, and positions the user's project within existing scholarship.

## When to Use

- Mapping relevant literature for a research project, grant proposal, or dissertation chapter
- Building an annotated bibliography with verified, hyperlinked citations
- Checking for gaps in an existing literature review
- Positioning a project within the scholarly landscape
- Identifying foundational and frontier work in a subfield

**Not for:** Biomedical/life-science reviews (use `literature-review` skill instead), single-paper summaries, or citation formatting only.

## Three Pillars (all required in every survey)

1. **Substantive** — theory and empirical findings on the core phenomenon.
2. **Empirical comparanda** — work using comparable data, cases, or designs. For theoretical/formal projects: empirical work that tests, validates, or motivates the theory.
3. **Methodological precedents** — papers establishing or applying the project's methods, measurement, or identification designs. For theoretical/formal projects: prior formal models, proof techniques, or analytical frameworks.

A survey missing any pillar is incomplete.

## Core Principles

### P1 — Project-first

Ground everything in the user's stated theme and RQ(s). Do not drift.

### P2 — Three pillars always

See above. Non-negotiable.

### P3 — Zero fabrication (overrides all other principles)

Never include a work whose existence is unverified. Never invent or guess any metadata (title, authors, year, venue, DOI, URL) or any content claim. **When in doubt, omit and log.**

**Anti-model-memory rule (critical).** Do not cite any paper based solely on training data. Every item in the bibliography must originate from one of these verified channels:
1. **Zotero** — retrieved via `zotero-mcp-code` skill (inherently verified).
2. **External API** — returned by OpenAlex, Crossref, Semantic Scholar, or Google Scholar with a resolvable identifier.
3. **Web retrieval** — found on a publisher site, SSRN, arXiv, or OSF with a stable URL.

If you "remember" a paper from training data, you **must** verify it via an external tool before including it. If verification fails, **drop and log** as "unverified model-memory item." This is the single largest source of hallucinated citations — treat model memory as untrusted input.

| Source | Trust level | Required verification |
|--------|------------|----------------------|
| Zotero | High (user-curated) | Metadata spot-check only |
| External API result | Medium | DOI resolution or cross-source check |
| Model memory / "I know this paper" | **Untrusted** | Must verify via API or web before inclusion |

### P4 — Every citation hyperlinked

Each citation must carry a clickable DOI (`https://doi.org/...`) or stable URL.

- **No DOI/URL found →** verify existence via ≥2 independent sources. If confirmed: include with `[No DOI/URL — verified via <source1, source2>]`. If unconfirmed: **drop and log**.
- **Never fabricate links.** Resolve uncertain DOIs; discard if resolution fails or mismatches.
- **Active DOI verification (required for non-Zotero items).** Do not assume a DOI is valid because it looks well-formed. Verify programmatically:
  1. **Crossref API:** `curl -s "https://api.crossref.org/works/{DOI}"` — confirm HTTP 200 and title match.
  2. **DOI resolution:** `curl -sI "https://doi.org/{DOI}"` — confirm redirect to publisher page (HTTP 302/301).
  3. **OpenAlex API:** `curl -s "https://api.openalex.org/works/doi:{DOI}"` — confirm metadata match.
- If the DOI does not resolve or metadata mismatches, **drop the DOI** and search Crossref (`https://api.crossref.org/works?query.bibliographic={title}`) for the correct one. If no valid DOI exists, apply the 2-source verification fallback above.

### P5 — Metadata confidence (required per item)

| Grade | Criteria |
|-------|----------|
| **A** | DOI resolves correctly; metadata matches trusted source (publisher, Crossref, OpenAlex). |
| **B** | Stable identifier; metadata consistent across ≥2 sources. No verified DOI. |
| **C** | Single non-canonical source or incomplete. Include only if no alternative exists and the item is the sole source for a needed concept/method/dataset; state what is unverified. |

When sources disagree on metadata, use publisher-of-record version and note discrepancy.

### P6 — Citation format

Consistent author-date with hyperlink. No style-switching within a survey.

- **Journal:** `[Author(s) (Year). "Title." *Venue*, Vol(Issue), pp.](DOI/URL)`
- **Book:** `[Author(s) (Year). *Title*. Publisher.](URL)`

Use "et al." for ≥3 authors in-text; list all authors in bibliography entries.

### P7 — Negative evidence required

Per cluster, ≥1 when available: null finding, replication/re-analysis, measurement critique.

### P8 — Reviews/handbooks

≥1 review or handbook chapter per major cluster when available.

### P9 — Foundational + frontier coverage

Every cluster must include both: (a) **foundational work** — the canonical statement; and (b) **frontier work** — most recent high-quality contribution (last 3–5 years). Label foundational works as `[Foundational]`.

## Source Rules

**Include:** peer-reviewed articles; books/chapters from major presses; review/handbook chapters; working papers passing the WP test.

**Exclude:** non-scholarly sources; predatory outlets; retracted papers (log if encountered).

### Working Paper Test

Include only if **≥2** of: (a) high citations/downloads for age; (b) established author; (c) top venue presentation; (d) sole source for needed dataset/method; (e) verifiable forthcoming/accepted. State which criteria met. Label as "Working paper / preprint."

## Quick Workflow Reference

The full 7-step workflow with substeps is in `references/workflow-reference.md`. Summary:

| Step | Action | Key gate |
|------|--------|----------|
| **0** | Understand project | Hard gate: RQ/topic must be clear. Confirmation gate before searching. |
| **1** | Retrieve | Zotero-first, then APIs + web. ≥15 candidates/track. |
| **2** | Citation-network expansion | 6–12 seed anchors, backward + forward via APIs. |
| **3** | Deduplicate | DOI match → normalized title+author+year. |
| **4** | Screen | Pillar tags + screening tags. Include if materially informing. |
| **5** | Quality control | Confidence grades, DOI verification, WP test. |
| **6** | Write deliverable | `rough_literature_survey.md`, 25–50 items. |
| **7** | Self-check | Internal consistency verification. Fix before finalizing. |

## Tools and Fallback Modes

| Mode | Resources | Note |
|------|-----------|------|
| 1 | **Zotero first** → then APIs + web | Full |
| 2 | APIs + web only | Note Zotero gap |
| 3 | User-supplied materials only | Note limitation prominently |

Try Mode 1 first. Fall back to Mode 2 only if Zotero MCP is unreachable. Log mode in Audit Log.

**Zotero-first rule:** Always search the user's Zotero library **first** using the `zotero-mcp-code` skill. Zotero results form the baseline; external sources then fill gaps.

## Deliverable Structure (Summary)

Full structure details in `references/workflow-reference.md`.

- **A) Project-to-Literature Map** (~1–2 pp): clusters, mechanisms, narrative synthesis, cross-cluster tensions
- **A1) Project Positioning:** gaps addressed, lines extended/challenged, contributions, scope conditions
- **A2) Concept Dictionary:** constructs, synonyms, operationalizations
- **A3) Dataset & Measurement Alignment Table** (if applicable)
- **B) Annotated Bibliography** (25–50 items): hyperlinked citations, pillar/confidence/screening tags, 3–6 bullets, connection notes
- **C) Must-Read Shortlist** (10–15): ≥80% Confidence A
- **D) Coverage Report:** sources, counts, confidence breakdown, limitations
- **E) Audit Log:** run date, queries, dropped/unlinked citations, retracted papers

## Chat Response (after writing deliverable)

Report only: (1) file confirmation, (2) item count by pillar, (3) cluster count, (4) top 5 must-reads with DOI links, (5) confidence breakdown, (6) link coverage, (7) project positioning summary, (8) suggested next steps. Do not reproduce file content in chat.

## Style

Concise, professional academic prose. Faithful, specific summaries grounded in verified text. No hedging about existence ("likely argues…", "may exist…"). Every content claim grounded in verified text. Defer to user's definitions over generic domain knowledge.

**Causal language discipline.** Reserve causal verbs for findings from designs warranting causal inference. Use "is associated with," "predicts," "correlates with" for observational work.

**Disagreement language.** State disagreements precisely: who claims what, on what evidence. Do not flatten into "the literature is mixed."

**Accessibility.** Write for a general political science audience. Define subfield jargon on first use. The survey should be usable as a draft lit review for a top generalist journal (APSR, AJPS, JOP).

## Context Budget Management

A full run can exceed context limits. Checkpoint intermediate state to `_lit_review_workspace.md` after Steps 2, 4, and 5. Process API responses immediately — extract needed fields and discard raw JSON. The deliverable file is the final checkpoint.

## Cross-Project Reuse

Check memory for prior surveys before Step 0. Reuse verified items (tag `[Prior survey: <filename>]`). Re-verify DOI/URLs for carried-over items. Update published WPs. Separate file per project.

## Memory

If persistent memory is available (e.g., agent memory directory or skill-level state), consult it at the start of every run and update at end of every run with: high-impact venues/authors; clusters and anchors; productive vs. unproductive queries; contested debates; WPs since published; cross-project links. Do not rely on the user to prompt a save.

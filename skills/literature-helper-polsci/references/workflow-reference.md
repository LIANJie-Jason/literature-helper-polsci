# Workflow Reference — Literature Helper (Political Science)

Detailed workflow steps, deliverable structure, and operational procedures for the literature-helper-polsci skill. Principles P1–P9 are defined in the main `SKILL.md` file.

---

## Step 0 — Understand the Project

**Hard gate (first).** Stop and ask if **RQ or topic** is missing/ambiguous. For causal/inferential projects, also stop if ID strategy or data sources are missing. For descriptive/exploratory projects, proceed if the descriptive scope is clear.

Extract and restate: topic; constructs + synonyms; RQ(s)/hypotheses; unit of analysis; data sources; ID strategy; methods; target journals.

**Ask for existing references.** After understanding the project, ask: "Do you have an existing reference list, bibliography, or partial lit review? If so, share it — I'll use it as a baseline and focus on gaps." If provided, ingest as a seed list (skip re-deriving known items; verify metadata; focus on what's missing).

### 0.1 Mechanisms vs. Estimands (for causal/inferential projects)

Mechanisms: 2–6 "because…" statements + observable implications. Estimands: unit, treatment, outcome, target estimand, moderators, ID strategy + assumptions, key threats. Flag mechanism–estimand mismatches.

For descriptive/exploratory projects, replace with: scope of description, key comparisons, and expected patterns.

### 0.2 Concept Dictionary

Per construct (6–12): label, synonyms, related-but-distinct concepts, operationalizations, data sources, units, measurement concerns.

### 0.2a Theoretical Frameworks

Identify 2–5 theoretical frameworks the project engages. For each: canonical statement (author, year), core predictions relevant to this project, and whether the project supports, extends, or challenges it. Ensure each framework's canonical statement is retrieved in Step 1.

### 0.3 Operationalizations Alignment

Constructs → measures, datasets, coverage limits, validity threats, validation/critique citations to find (placeholders OK until Step 5).

### 0.4 Search Plan

- **Track A (Substantive):** 8–12 queries + 5–10 seed authors/venues.
- **Track B (Empirical):** 4–6 queries.
- **Track C (Methods):** 4–6 queries.
- **Cross-disciplinary check:** If the topic spans disciplines (common in computational social science), include ≥2 queries targeting adjacent fields (economics, sociology, computer science, communication, area studies). Note non-English scholarship gaps if the topic has a regional dimension.

### Confirmation Gate (last in Step 0)

Present to the user: (1) restated RQ and project scope, (2) concept dictionary, (3) search plan with all query strings. Ask: "Does this capture your project correctly? Should I adjust any constructs, queries, or scope before searching?" **Wait for confirmation or corrections** before proceeding to Step 1. For incremental updates (Step 6), present only what changed since the prior survey.

---

## Step 1 — Retrieve

### 1a) Zotero Search (mandatory first action)

Search the user's Zotero library using the `zotero-mcp-code` skill for each track's queries. Tag all Zotero-sourced items as `[From Zotero]`. These form the baseline.

### 1b) External API + Web Search

Search OpenAlex, Crossref, Semantic Scholar via their APIs. For Google Scholar and general web search, use available web search tools (e.g., `perplexity-search`, `research-lookup`, or `deep-research` MCP). Deduplicate against Zotero results before adding.

Target ≥15 candidates/track (≥45 total, combining Zotero + external).

### Book Coverage Check

API-based search (OpenAlex, Crossref, Semantic Scholar) systematically underrepresents books and edited volumes. For book-heavy subfields (comparative politics, political theory, area studies), supplement API results with: publisher catalogue searches, handbook reference lists, and Google Scholar book results. If the topic plausibly has major book-length treatments, actively search for them.

### If Retrieval Falls Short

If <10 per track: broaden queries (relax scope, try synonyms, expand to adjacent disciplines); attempt additional seed authors; try different APIs. If still thin, note in Coverage Report — do not fabricate items to fill gaps.

---

## Step 2 — Citation-Network Expansion

Select 6–12 seed anchors from Step 1 (prioritize must-read candidates and foundational works). Expand backward (references) and forward (cited-by). Stop when duplicate-heavy. Document seeds and expansion counts.

**Required tools for citation-network traversal (do not rely on model memory):**

- **Semantic Scholar API (preferred):** `https://api.semanticscholar.org/graph/v1/paper/{DOI}/references` and `.../citations` — returns structured reference/citation lists with DOIs. Free, no key required for moderate use.
- **OpenAlex API:** `https://api.openalex.org/works?filter=cites:{work_id}` (forward) and `referenced_works` field (backward) — broad coverage, free.
- **Crossref API:** `https://api.crossref.org/works/{DOI}` — `reference` field for backward citations.

**Process:** For each seed anchor, retrieve its reference list and citing papers via API. Extract DOIs/titles from API responses. Cross-reference against existing candidate pool. Add new items that appear relevant (title/abstract match to project scope). All new items from expansion must go through Step 5 (Quality Control) — they carry the same verification burden as any non-Zotero item.

**Do not** reconstruct citation networks from memory. If an API call fails for a specific seed, note it in the Audit Log and move to the next seed.

---

## Step 3 — Deduplicate

Match: DOI (exact) → normalized title + first-author + year. Keep strongest-metadata record. Log counts.

---

## Step 4 — Screen

Include items materially informing any track — the item's argument, data, design, or finding directly bears on the user's RQ(s), constructs, methods, or empirical setting. Exclude items that mention a keyword but lack substantive relevance.

When borderline: include if the item is the only source on a needed sub-topic; otherwise exclude and log.

Apply pillar tags (`[Substantive]`/`[Empirical]`/`[Methodological]`) and screening tags (`[Null/Negative evidence]`, `[Contradictory evidence]`, `[Replication/Reanalysis]`, `[Measurement critique/validation]`).

---

## Step 5 — Quality Control

- Assign Metadata Confidence (A/B/C) per P5.
- Resolve DOI/URL per P4. Drop unverifiable items.
- Apply WP test; state criteria met.
- Metadata conflicts → use publisher-of-record; note discrepancy.
- Retracted → exclude and log.
- Abstract-only → label "abstract-based." Abstract-based items may not appear in must-read (Section C) unless no full-text alternative exists for a critical concept.

---

## Step 6 — Write Deliverable

Create/overwrite **`rough_literature_survey.md`** in the current working directory (or the user's specified project directory). Target **25–50 items**. If <25, explain which tracks were thin and why. If >50, explain why broader coverage was needed.

### Incremental Update Mode

If a prior survey exists for the same project (check memory + ask user): (1) read the existing file; (2) verify all existing items still have valid links and metadata; (3) add new items found in this run; (4) update the map, positioning, and coverage report; (5) mark new additions with `[New: <date>]`. Log "incremental update" in the Audit Log.

---

## Step 7 — Self-Check (required before reporting to user)

Verify internal consistency:

- Every must-read item (Section C) appears in the bibliography (Section B).
- Every cluster in the map (Section A) has ≥2 items in the bibliography. Clusters with only 2 items: flag as **thin** in the Coverage Report — these may represent blind spots or emerging areas.
- All three pillars are represented in both bibliography and must-read.
- Every hyperlink is well-formed (starts with `https://`).
- No entry lacks both a hyperlink and a `[No DOI/URL — verified via...]` tag.
- Pillar balance: no single pillar exceeds 60% of bibliography. If imbalanced, explain in Coverage Report.
- Project Positioning (A1) references specific bibliography items.
- Each identified theoretical framework has its canonical statement in the bibliography.

Fix any failures before finalizing.

---

## Deliverable Structure (Full)

### A) Project-to-Literature Map (~1–2 pp)

Restate project and RQ(s). Identify literature clusters across all three pillars (typically 4–8; fewer or more OK with justification). Per cluster:

- Mechanisms addressed; estimand/design problems informed; maturity (nascent / growing / established — based on volume, replication, and methodological convergence); canonical datasets.
- **Narrative synthesis:** Do not just list papers. Write connected prose showing how ideas evolved, where scholars agree/disagree, and what the current frontier looks like. Identify cross-cluster tensions and connections.
- **Disagreements / Nulls / Critiques** sub-block.

**Cross-cluster synthesis (required, at end of Section A).** After all clusters, write 1–2 paragraphs identifying: (a) how clusters connect or depend on each other; (b) tensions between clusters (e.g., theory in Cluster 1 predicts X, but empirical work in Cluster 3 finds ¬X); (c) what the combined landscape implies for the user's project.

### A1) Project Positioning

Explicitly state: (a) which gap(s) in the literature the user's project addresses; (b) which existing lines of work it extends, challenges, or combines; (c) what the project contributes that is not already in the literature; (d) **scope conditions** — under what contexts (geographic, temporal, institutional, methodological) the project's claims are expected to hold, and where they may not generalize, based on the literature reviewed. This section justifies the project's novelty and is the most valuable part of the survey for grant proposals and paper introductions.

### A2) Concept Dictionary

Updated from Step 0.2. Same schema, refined with retrieval findings.

### A3) Dataset & Measurement Alignment Table (if applicable)

Include if the project uses named datasets, indices, or measurement instruments. Skip for purely theoretical, formal-model, or qualitative projects — state why omitted.

| Dataset | Construct(s) | Unit | Measurement logic | Coverage | Validity threats | Validation/critique citations |
|---------|-------------|------|-------------------|----------|-----------------|-------------------------------|

4–20 rows (justify if <4). Link to `[Measurement critique/validation]` items.

### B) Annotated Bibliography

Per item:

- Hyperlinked citation (P6). No link → `[No DOI/URL — verified via <sources>]`. **Unverified items must not appear.**
- Type (journal article / book / book chapter / review article / handbook chapter / working paper / conference paper). Pillar tag(s). Confidence (A/B/C; rationale if B/C). Screening tags.
- WP criteria met (if applicable).
- 3–6 bullets: RQ/argument; data/design; findings (causal language only if design warrants); project relevance; limitations. Use 3 bullets for peripheral items; 5–6 for anchors and must-reads.
- **Content claims must be drawn from verified text** (abstract, full text, or trusted secondary source). If only metadata available, state "summary based on [source]" or limit to what is verifiable. Never infer findings from title alone. For abstract-only items, note `[Summary based on abstract]`.
- **Connection note:** 1 sentence linking this item to ≥1 other item in the bibliography (agrees with, extends, contradicts, uses same data as, applies method from). This prevents the bibliography from reading as an isolated list.

Must include `[Null/Negative evidence]`, `[Contradictory evidence]`, `[Replication/Reanalysis]`, `[Measurement critique/validation]` items when such work exists.

### C) Must-Read Shortlist (10–15)

1-line justification + pillar + confidence + hyperlinked DOI/URL. ≥80% Confidence A; remainder B; C only for frontier WPs with justification.

### D) Coverage Report

- Sources + dates. Counts per track: retrieved → deduped → screened → final.
- Citation-network expansion: seeds, directions, additions.
- Confidence breakdown (A/B/C).
- **Link resolution:** DOI / non-DOI URL / verified-unlinked / dropped-as-unverifiable.
- **Theoretical framework coverage:** Which frameworks are well-represented, thinly covered, or absent.
- Negative-evidence counts; clusters where none found.
- Limitations.

### E) Audit Log

- Run date; project; tool mode.
- Queries (1 line each, grouped A/B/C).
- Dedup counts.
- Citation-expansion summary.
- **Dropped citations:** title, authors, reason.
- **Unlinked citations:** title, verification sources.
- **Retracted papers** encountered.
- Stopping rationale.

---

## Context Budget Management

1. **Checkpoint to file after each major step.** Save intermediate state to `_lit_review_workspace.md` in the working directory. Checkpoints at: end of Step 2 (candidate list after retrieval + citation expansion), end of Step 4 (screened pool with pillar tags), end of Step 5 (QC-verified items ready for writing). Each checkpoint overwrites the previous — only the latest stage matters.
2. **Process API responses immediately.** When calling Crossref, OpenAlex, Semantic Scholar, or Zotero APIs, extract only the needed fields (title, authors, year, DOI, venue, abstract snippet) and discard the raw JSON. Never hold full API responses in conversation.
3. **If context feels long mid-run,** compact before continuing: write current state to the checkpoint file, summarize progress in 3–5 sentences, then proceed from the checkpoint. Do not attempt to hold all candidates in conversation simultaneously.
4. **The deliverable file is the final checkpoint.** Workspace file can be deleted after Step 6.

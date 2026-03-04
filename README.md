# literature-helper-polsci

A Claude Code skill for producing curated, citation-verified literature surveys for political science and computational social science research projects.

## What it does

- Retrieves, verifies, screens, and synthesizes scholarship into a traceable, hyperlinked deliverable (`rough_literature_survey.md`)
- Enforces a **zero-fabrication** policy with anti-model-memory rules — every citation must be externally verified
- Covers three required pillars: Substantive theory, Empirical comparanda, and Methodological precedents
- Produces narrative-driven surveys (not just lists) that position your project within existing scholarship

## Installation

Install as a Claude Code plugin from GitHub:

```bash
# Step 1: Add the plugin from GitHub
claude plugin marketplace add LIANJie-Jason/literature-helper-polsci

# Step 2: Install it
claude plugin install literature-helper-polsci
```

## Structure

```
literature-helper-polsci/
├── .claude-plugin/
│   ├── marketplace.json        # Repo-level plugin index
│   └── plugin.json             # Plugin manifest
├── skills/
│   └── literature-helper-polsci/
│       ├── SKILL.md            # Main skill (principles, workflow summary)
│       └── references/
│           └── workflow-reference.md  # Detailed 9-step workflow
├── README.md
└── LICENSE
```

## Dependencies

This skill works best with:

- **Zotero MCP** — for searching your personal Zotero library (Zotero-first retrieval)
- **`zotero-mcp-code` skill** — for efficient multi-strategy Zotero searches
- **Web search tools** — `perplexity-search`, `research-lookup`, or `deep-research` MCP for external retrieval

The skill degrades gracefully if Zotero is unavailable (falls back to API + web only mode).

## Workflow

| Step | Action |
|------|--------|
| 0 | Understand project (RQ, constructs, search plan) |
| 1 | Retrieve (Zotero-first, then APIs + web) |
| 2 | Citation-network expansion |
| 3 | Deduplicate |
| 4 | Screen and tag by pillar |
| 5 | Quality control (confidence grades, DOI verification) |
| 6 | Write deliverable |
| 7 | Self-check for consistency |
| 8 | Iterative validation loop |

## Output

The skill produces `rough_literature_survey.md` containing:

- **Project-to-Literature Map** with cluster analysis and cross-cluster synthesis
- **Project Positioning** identifying gaps, contributions, and scope conditions
- **Annotated Bibliography** (25–50 items) with hyperlinked citations and confidence grades
- **Must-Read Shortlist** (10–15 items)
- **Coverage Report** and **Audit Log** for full transparency

## Caveats

- **You should always read the papers themselves,** and this tool is merely a starting point.
- Despite all the guardrails, I suggest you to debug the final reports one to two times manually on both the accurate **citations** and the correct **summarizations**. 

## License

MIT

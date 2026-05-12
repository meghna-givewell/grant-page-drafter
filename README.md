# /grant-page-drafting

A Claude Code skill that drafts public-facing GiveWell grant pages from internal conditional approval (CA) documents.

## What it does

Given a conditional approval document, this skill:

1. Reads the CA and extracts key details (grantee, grant type, foreign aid relevance, CEA link)
2. Loads GiveWell's style guide, legibility guidance, citation standards, and relevant example pages
3. Asks two setup questions: whether you have a source documents folder and any external web links to read
4. Scans the CA's own footnotes to build an initial sources list
5. Drafts each section of the grant page in order, with inline citation markers (`[1]`, `[2]`, etc.) and a two-column Sources table
6. Runs a citation audit pass — sentence by sentence — to catch uncited factual claims
7. Flags potentially sensitive content with `[[POTENTIALLY SENSITIVE]]`
8. Outputs a formatted Google Doc with a Drafter's Review section listing gaps, missing citations, and flags for researcher attention

> **Note on footnotes:** The draft uses `[1]`, `[2]` etc. as placeholder markers. Before publication, convert each to a real Google Doc footnote (Cmd+Option+F on Mac) using the Sources table as reference. The Sources table can then be removed.

## Output

A Google Doc titled `Grant Page Draft — [Grant Name] — [Date]`, shared with the researcher's Google account. The doc includes all standard grant page sections plus a Drafter's Review at the end.

## How to use

In a Claude Code session with the Hardened Google Workspace MCP connected:

```
/grant-page-drafting <CA-URL> [<CEA-URL>]
```

**Example:**
```
/grant-page-drafting https://docs.google.com/document/d/1abc.../edit
```

The CEA URL is optional but enables a richer cost-effectiveness section if provided.

## Requirements

- [Claude Code](https://claude.ai/code) with this skill installed at `~/.claude/skills/grant-page-drafting/`
- [Hardened Google Workspace MCP](https://github.com/c0webster/hardened-google-workspace-mcp) configured and authenticated
- Access to the CA document in Google Drive

## Installation

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/meghna-givewell/grant-page-drafter.git \
  ~/.claude/skills/grant-page-drafting
```

## What the skill produces section by section

| Section | Notes |
|---|---|
| In a Nutshell | 1–3 paragraphs; bulleted reasons and reservations; no inline citations |
| The organization | Grantee background and track record |
| The intervention | Omitted for top charity renewals with existing GiveWell IR |
| The grant | Activities, budget table, theory of change; contingency tranches flagged |
| The case for the grant | Qualitative reasons + Simple CEA table with 25th–75th percentile ranges |
| Risks and reservations | 3–6 specific, falsifiable reservations |
| Plans for follow-up | Milestones and check-in cadence from the CA |
| Internal forecasts | Always included; pulled from CA or flagged as `[FORECAST NEEDED]` |
| Our process | How GiveWell evaluated the grant |
| Relationship disclosures | Defaults to "None." with researcher verification note |
| Sources | Two-column table (Document \| Source); unpublished sources noted |
| Drafter's Review | Lists all `[SOURCE NEEDED]`, `[FORECAST NEEDED]`, stubs, inconsistencies, and sensitivity flags |

## Tips for better output

- **Source documents folder:** create a Google Drive folder for the grant, upload key PDFs and reports, and share the link when prompted. Claude will read those files and use them to verify statistics and fill in citation metadata.
- **External links:** paste any relevant URLs (WHO reports, government data, evaluator websites) when prompted. Claude will fetch what it can.
- **CEA:** provide the CEA URL upfront or when prompted — it enables the Simple CEA table to be populated with real numbers.

## Reference documents used

Loaded automatically from `reference_docs/LINKS.md` at the start of each session:

- GiveWell Style Guide
- Legibility Guidance (grant pages, IRs, Simple CEAs)
- Grant Page Template
- GiveWell Citations Guide
- Types of Non-Cited Statements
- Language and Structure Guide
- 2–3 example grant pages matched to the grant type

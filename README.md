# /grant-page-drafting

A Claude Code skill that drafts public-facing GiveWell grant pages from internal conditional approval (CA) documents.

## What it does

Given a conditional approval document, this skill runs a structured pipeline before, during, and after drafting:

### Before drafting
1. Reads the CA and infers grant name, type, foreign aid relevance, and CEA status — asks you to confirm before continuing
2. Asks for a Google Drive source documents folder and any external web links to read
3. Reads the **full content** of every source document (Drive files, web pages, CEA spreadsheet) before writing a word
4. Scans every CA footnote to identify the underlying citable sources and build a ready-to-use Sources table
5. Builds a **source-claim mapping table** — maps every intended factual claim to a source, page number, and supporting quote before prose begins; uncited claims are flagged before drafting, not after

### Drafting
6. Drafts each section in order with inline `[N]` citation markers and a two-column Sources table containing ready-to-use footnote text

### After drafting (pre-publish checks)
7. Runs a three-step citation audit:
   - **Source-claim reconciliation** — verifies every row in the pre-draft mapping table made it into the draft with a marker
   - **Sentence-by-sentence audit** — checks every body sentence for uncited factual claims
   - **Citation format audit** — verifies every Sources table row is correctly formatted per GiveWell Citations Guide
8. Scans for potentially sensitive content and flags with `[[POTENTIALLY SENSITIVE]]`

### Multi-agent verification pipeline
9. **Phase 2 (parallel):** Three independent agents review the Google Doc simultaneously:
   - *Critic* — checks for uncited claims, factual mismatches, missing content, source table errors, and unused sources
   - *Numbers Verifier* — extracts every number and traces each to its source document
   - *Budget Arithmetic Verifier* — checks that budget line items sum to the stated total and match the grant amount in prose
10. **Phase 3:** Editor agent fixes all Phase 2 findings and applies style and legibility checks
11. **Phase 4 (parallel):** Two more independent agents:
    - *Critic 2* — second-pass review for residual issues and cross-section inconsistencies (same fact stated differently in different sections)
    - *External Reader* — reads as an educated outsider; flags unclear reasoning, unexplained jargon, and unanswered questions
12. **Phase 5:** Final Editor makes remaining corrections and writes the Drafter's Review
13. **Phase 6:** Reports the Google Doc link, a summary of fixes made, and what still needs researcher attention

> **Note on footnotes:** The draft uses `[1]`, `[2]` etc. as placeholder markers. Before publication, convert each to a real Google Doc footnote (Cmd+Option+F on Mac) using the Sources table as reference. The Sources table can then be removed.

> **Note on sources:** The CA itself is **not** a citable source. The skill cites the underlying sources the CA draws from — grant proposals, program documents, evaluator reports, research papers, grantee communications.

## Output

A Google Doc titled `Grant Page Draft — [Grant Name] — [Date]`. The doc includes all standard grant page sections, plus a Drafter's Review at the end listing what was fixed across editing rounds, what still needs researcher attention, and any potential inconsistencies the researcher should clarify before publication.

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
| Sources | Two-column table (Document \| Source) with ready-to-use footnote text; unpublished sources noted |
| Drafter's Review | Summary of all fixes made; remaining `[SOURCE NEEDED]`, `[FORECAST NEEDED]`, stubs, sensitivity flags, and inconsistencies |

## Tips for better output

- **Source documents folder:** Create a Google Drive folder for the grant, upload key PDFs and reports (grant proposal, program design document, evaluator reports), and share the link when prompted. The skill reads the full content of every file before drafting — this is the single biggest driver of citation quality.
- **External links:** Paste any relevant URLs (WHO reports, government data, evaluator websites) when prompted. Claude will fetch what it can.
- **CEA:** Provide the CEA URL upfront or when prompted — it enables the Simple CEA table to be populated with real numbers and allows the Numbers Verifier to cross-check cost-effectiveness figures.

## Reference documents used

Loaded automatically from `reference_docs/LINKS.md` at the start of each session:

- GiveWell Style Guide
- Legibility Guidance (grant pages, IRs, Simple CEAs)
- Grant Page Template
- GiveWell Citations Guide
- Types of Non-Cited Statements
- Language and Structure Guide
- 2–3 example grant pages matched to the grant type

# Changelog

All notable changes to LOSTWARE are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [7.5-S — Deep Research] — 2026-06-26

Experimental variant. Higher token cost. Designed for reasoning models with extended thinking.

### Added
- Two-phase pipeline: Phase 4.1 (broad prospecting) → Phase 4.2 (strategic deep dive toward best source vein).
- Internal Research Ledger: candidate titles tracked across search rounds before scoring.
- Granular numeric scoring engine with explicit arithmetic (+2/+1/-3/-2/-1) replacing qualitative classification.
- Four-tier classification: `[PRIMARY-HIGH]` (≥5) / `[PRIMARY]` (3–4) / `[BORDERLINE]` (1–2) / `[MAINSTREAM]` (≤0).
- Genre Purity enforcement in Phase 4.3: genre mismatch is an instant DROP, no exceptions.
- Score visible in output per candidate: `Score: [X]`.
- Final Self-Audit block (section 6): three internal checks before any output is written.

### Changed
- Scoring positive indicators expanded to six criteria vs. five in V7.5-MINI.
- Node independence definition made explicit: two URLs on the same root domain count as one node.

---

## [7.5-MINI] — 2026-06-26

### Added
- Cultural anchors for obscurity calibration: explicit OBSCURE and MAINSTREAM reference titles to anchor the model's judgment.
- Five mandatory query templates (T1–T5) covering archives, forums, magazines, preservation indexes, and MobyGames cross-reference.
- SCRATCHPAD directive: model must tag each candidate as `[PRIMARY]`, `[BORDERLINE]`, or `[MAINSTREAM]` internally before output.
- GENRE PURITY rule: explicit prohibition on broadening or substituting the target genre during search.
- Mandatory Discarded paragraph in output: model must name and justify every excluded mainstream title.
- FINAL VERIFICATION block (section 6): three checks before output is delivered.
- Availability emoji indicators: 🟢 Accessible / 🟡 Hard to find / 🔴 Possible Lost Media.

### Changed
- Hard Gatekeeper changed from conversational (asking the user) to silent extraction: model reads parameters from input directly, no back-and-forth.
- Output format changed from free Markdown table (V6.2) to fixed Case A / Case B schema.
- Genre expansion on dry results removed. Genre purity now enforced strictly — no broadening allowed.
- Pivot protocol (subgenre → platform → neighbor region) removed. Depth over breadth.
- Output language locked to English regardless of user input language (V5.8-MINI and V6.2 mirrored user language).

### Removed
- Native Language Map (hardcoded region-to-language table from V5.8-MINI). Replaced by instruction to identify the region's primary official language dynamically.
- Deficit Protocol (automatic expansion when <5 titles found). Replaced by CASE A clean failure.
- CORROBORATED and UNKNOWN evidence tiers from V6.2. Simplified to CONFIRMED / UNCONFIRMED.
- User-overridable platform scope. Scope is now fixed.

---

## [6.2] — 2026-06-25

### Added
- Explicit gaming culture context instruction: model instructed to consider demoscene, BBS networks, and magazine press per region before searching.
- Four evidence tiers: Confirmed / Corroborated / Unconfirmed / Unknown.
- Node counting rule clarified: subdomains of the same root domain count as one node.
- Pivot protocol on dry results: subgenre → platform → neighboring region, max 3 rounds.
- Genre expansion ladder on zero results: specific term → era synonym → broad genre.
- Single-line failure report with search and pivot count when nothing is found.
- Output in Spanish (hardcoded).

### Changed
- Gatekeeper changed from BOOT sequence (V5.8-MINI) to inline check: model extracts parameters from input, asks only if missing.
- Output format changed from field-per-line to Markdown table with fixed columns.
- Search order made explicit: native language first, English only if local queries fail.

### Removed
- Native Language Map. Replaced by general instruction to start with the region's native language.
- Fabrication Traps block. Anti-hallucination logic moved into evidence evaluation prose.
- Deficit Protocol. Replaced by Pivot Protocol with different logic.
- SEARCH MANDATE block. Anti-fabrication rules embedded in "Do not invent titles" instruction.

---

## [5.8-MINI] — 2026-06-24

First version with systematic anti-hallucination controls.

### Added
- BOOT sequence: model asks for GENRE, REGION, and PERIOD before any search.
- Native Language Map: hardcoded table mapping 15 regions to their primary language.
- SEARCH MANDATE: every candidate must come from a `web_search` tool call in the current session. Training memory as evidence is explicitly prohibited.
- Three Fabrication Traps: no archive.org URL without live search result, no native search claimed without tool call, no titles invented to reach a count target.
- GATE: three conditions required simultaneously for any title to pass (retrieved via tool, ≥2 independent nodes, explicit genre evidence).
- Deficit Protocol: automatic expansion to subgenre, platform, or neighboring region when fewer than 5 titles found.
- Native enforcement with fallback: if native query returns zero results, one reformulation attempt is allowed before marking the node as EXHAUSTED.
- Output includes exact query string passed to web_search tool, not reconstructed.
- Output language mirrors user input language.


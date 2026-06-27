# # Scoring Engine CORE-1
## Technical Specification

**Version:** CORE-1 (v1.0b)  
**Status:** Stable / Production  
**Last Updated:** 2026-06-27

---

## 1. Overview

The Scoring Engine is the heart of LOSTWARE Core 1. It transforms raw extracted game titles into categorized, actionable intelligence (CONFIRMED, UNCONFIRMED, or DISCARDED). 

Unlike traditional numeric scoring systems, the Core 1 engine uses a **deterministic Boolean heuristic** combined with **multi‑source node verification**. This ensures maximum transparency: every classification decision can be traced back to a specific rule and a specific snippet.

---
```
## 2. The Scoring Pipeline
[Extracted Title + Metadata + Snippets + Sources]
│
▼
┌───────────────────────┐
│ 1. BRUTAL PRE-FILTER │ (Platform, Year, Mainstream signals)
└───────────────────────┘
│ (Pass)
▼
┌───────────────────────┐
│ 2. POSITIVE INDICATOR │ (Check for obscurity evidence)
│ SCAN (≥1 required) │
└───────────────────────┘
│ (Found)
▼
┌───────────────────────┐
│ 3. NEGATIVE INDICATOR │ (Check for mainstream contamination)
│ SCAN (0 allowed) │
└───────────────────────┘
│ (None found)
▼
┌───────────────────────┐
│ 4. NODE VERIFICATION │ (Count independent root domains)
└───────────────────────┘
│
▼
┌───────────────────────┐
│ 5. SELF-AUDIT LOOP │ (Re-scan snippets for missed signals)
└───────────────────────┘
│
▼
FINAL CLASSIFICATION

```

---

## 3. Heuristic Scoring (Boolean Logic)

### 3.1 Positive Indicators (Obscurity Evidence)

A title **passes** this stage if **at least one (≥1)** of the following conditions is met:

| ID | Indicator | Source Pattern |
|:---|:---|:---|
| **P1** | Regional Magazine Scan | URL/snippet references a physical magazine from the target region (e.g., *Hobby Computer* in Spain, *Bajtek* in Poland). |
| **P2** | Local Retro Forum | URL contains forum domain AND snippet uses region‑specific language (e.g., `forum.polish-retro.pl`, `sg.hu` for Hungary). |
| **P3** | BBS / FTP / Shareware CD | Snippet mentions `BBS`, `FTP directory`, `CD compilation`, `cover disk`, or `shareware` distribution. |
| **P4** | Unreleased / Prototype / Beta | Snippet explicitly contains: `prototype`, `beta`, `cancelled`, `unreleased`, or `lost media`. |
| **P5** | Preservation Index | Source is `archive.org`, `abandonware` site, or an emulation preservation database with **no commercial reviews**. |

**Logic Gate:**
HAS_POSITIVE = (P1 OR P2 OR P3 OR P4 OR P5)

```
If `HAS_POSITIVE` is **FALSE** → Title automatically goes to **UNCONFIRMED** (single weak source) or **DISCARDED** (if no sources at all).
```
---

### 3.2 Negative Indicators (Mainstream Signals)

A title **fails** (is discarded) if **at least one (≥1)** of the following conditions is met:

| ID | Indicator | Detection Pattern |
|:---|:---|:---|
| **N1** | Digital Storefront | Snippet contains: `Steam`, `GOG`, `Epic Games`, or any modern digital re‑release platform. |
| **N2** | Encyclopedic Presence | Snippet explicitly mentions `Wikipedia` or `Fandom` as a primary source. |
| **N3** | Modern Re‑release | Snippet contains: `remaster`, `remake`, `re-release`, `modern port`, or `digital re-release`. |
| **N4** | Global “Best Of” Lists | Snippet contains: `top 10`, `best of`, `classic collection` in a commercial context. |

**Logic Gate:**
HAS_NEGATIVE = (N1 OR N2 OR N3 OR N4)

```
If `HAS_NEGATIVE` is **TRUE** → Title is immediately classified as **DISCARDED** (regardless of positive indicators).
```
---

### 3.3 Decision Matrix (Pre‑Verification)

| HAS_POSITIVE | HAS_NEGATIVE | Pre‑Verification Status |
|:---:|:---:|:---|
| TRUE | FALSE | Pass → Proceed to Node Verification |
| TRUE | TRUE | **DISCARD** |
| FALSE | FALSE | Weak pass → Proceed to Node Verification (will likely become UNCONFIRMED) |
| FALSE | TRUE | **DISCARD** |

---

## 4. Node Verification (Multi‑Source Rule)

The final distinction between **CONFIRMED** and **UNCONFIRMED** depends on source diversity:
COUNT_UNIQUE_DOMAINS = number of unique root domains (e.g., archive.org, forum-x.com, retro-y.pl)


| Unique Domains | Classification |
|:---|:---|
| **≥ 2** | **CONFIRMED** (Obscure based on regional context) |
| **1** | **UNCONFIRMED** (Single weak source) |

**Rationale:** A single blog or personal list can be erroneous. Two independent, cross‑referenced sources provide sufficient archaeological confidence for digital preservation.

---

## 5. Self‑Audit Loop (Validation)

After classification, the engine performs a **final re‑scan** of all snippets associated with the title:

1. Search for any missed **Negative Indicators** (N1–N4) that might have been overlooked.
2. If any are found → Re‑classify as **DISCARDED**.
3. If none are found → Keep the classification (CONFIRMED or UNCONFIRMED).

This prevents “sneaky” mainstream signals from contaminating the output.

---

## 6. Tunable Parameters (For Developers)

You can modify the scoring logic by editing the following constants in the prompt:

| Parameter | Description | Default Values |
|:---|:---|:---|
| **Positive_List** | Add new obscurity signals | `magazine`, `forum`, `BBS`, `FTP`, `prototype`, `archive.org` |
| **Negative_List** | Add new mainstream signals | `Steam`, `GOG`, `Wikipedia`, `remaster`, `top 10` |
| **Platform_Whitelist** | Acceptable platforms | `PC`, `DOS`, `Amiga`, `Atari ST`, `C64`, `ZX Spectrum`, `Windows 95` |
| **Platform_Blacklist** | Auto‑discard platforms | `SNES`, `PS1`, `N64`, `Sega`, `Genesis`, `Arcade`, `Xbox`, `Mobile` |
| **Era_Range** | Acceptable release years | `1989 – 1999` |
| **Min_Domains_For_Confirmed** | Required independent sources | `2` |

---

## 7. Example Walkthrough

**Input Title:** *“Kret”* (extracted from a Polish forum)  
**Snippet:** *“Kret (1995) — found in Polish shareware CD compilation, also available on GOG.”*

1. **Pre‑Filter:** Platform not mentioned → `UNKNOWN` (kept). Year `1995` → within 1989‑1999 (kept).
2. **Positive Scan:** `P3` (shareware CD) found → `HAS_POSITIVE = TRUE`.
3. **Negative Scan:** `N1` (GOG) found → `HAS_NEGATIVE = TRUE`.
4. **Decision Matrix:** `TRUE + TRUE` → **DISCARD**.
5. **Output:** Added to `# Discarded` section with reason: *“available on GOG”*.

---

**Input Title:** *“Strategos”* (extracted from Polish Amiga forum)  
**Snippet:** *“Strategos (1993) — Amiga TBS indexed at OldGames.sk and Polish Amiga preservation forums (PPA).”*

1. **Pre‑Filter:** Platform `Amiga` → Valid. Year `1993` → Valid.
2. **Positive Scan:** `P2` (local forum) and `P5` (preservation index) found → `HAS_POSITIVE = TRUE`.
3. **Negative Scan:** No `Steam`, `GOG`, `Wikipedia`, or `remaster` → `HAS_NEGATIVE = FALSE`.
4. **Pre‑Verification:** `TRUE + FALSE` → Pass.
5. **Node Verification:** Domains: `oldgames.sk` and `ppa.pl` → **≥2**.
6. **Classification:** **CONFIRMED**.
7. **Output:** Added to `# Confirmed` section with context linking both sources.

---

## 8. Quick Reference Card

| Stage | Condition | Action |
|:---|:---|:---|
| **Pre‑Filter** | Platform invalid / Year out of range | **DISCARD** |
| **Positive** | 0 indicators | UNCONFIRMED (weak) |
| **Positive** | ≥1 indicator | Proceed |
| **Negative** | ≥1 indicator | **DISCARD** |
| **Negative** | 0 indicators | Proceed |
| **Nodes** | ≥2 domains | **CONFIRMED** |
| **Nodes** | 1 domain | **UNCONFIRMED** |
| **Self‑Audit** | New negative found | Re‑classify as **DISCARDED** |

---

## 9. Notes on Extending the Engine

To adapt the engine for new regions or genres:

- **Add regional signals:** Include local BBS names (e.g., `FidoNet`, `HobbyNet`) or specific magazine titles (e.g., `Peke` for Spain, `Commodore` for UK) to the **Positive_List**.
- **Add new mainstream channels:** If a game appears on `itch.io` or `Game Pass`, add those to the **Negative_List**.
- **Adjust the Era:** Change the `Era_Range` to 1980‑1995 if targeting earlier systems.
- **Increase verification strictness:** Set `Min_Domains_For_Confirmed = 3` for high‑stakes research.

The engine is designed to be **human‑readable** and **deterministic**—every classification can be mathematically traced back to its source snippets.
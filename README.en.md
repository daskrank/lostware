# LOSTWARE

[![Version](https://img.shields.io/badge/version-5.7--MINI-blue.svg)](#)
[![Type](https://img.shields.io/badge/type-Structured--Prompt-orange.svg)](#)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Models](https://img.shields.io/badge/models-DeepSeek%20%7C%20Qwen%20%7C%20GLM-purple.svg)](#)

**Technical description:** A set of structured instructions that guide the execution of web search tools by Language Models (LLMs), optimizing the retrieval of niche historical information through heuristic filtering and source auditing.

**Simple description:** Digital archaeology prompt to find lost, regional, or forgotten computer video games from different eras, filtering out mainstream or widely known titles.

---

## How to use

1. Copy the contents of the file [`prompts/lostware-v5.7-mini.md`](prompts/lostware-v5.7-mini.md)
2. Paste it into the chat window of your chosen language model.
Recommended model: [DeepSeek](https://chat.deepseek.com).
3. Ensure the model has active access to a web search tool.
4. When prompted, enter your query in a single line indicating three parameters:
   - Genre
   - Region
   - Period (optional, default: 1989–1999)

[genre] [country or region] [period]


**Example:**
Shoot em up de Korea


The model will execute the search autonomously and return the list of findings. You can repeat queries within the same session.

---

## How it works

```
[Input: GENRE + REGION]
        │
        ▼
[Hard Gatekeeper] ──── missing parameter ────► [HALT + error msg]
        │
    (valid)
        ▼
[Native templates search T1–T5]
        │
        ▼
[Obscurity indicator audit] ──── score ≤ 0 ────► [Drop]
        │
    (aprobado)
        ▼
[Node verification: ≥2 independent domains]
        │
        ▼
[internal Self-Audit → Structured Output]
```

**Key components:**

**Hard Gatekeeper** — If the user does not provide `GENRE` and `REGION`, the prompt halts execution before any search. It prevents hallucinations caused by incomplete context.

**Native search templates (T1–T5)** — Queries are generated in the official language of the target region. This accesses local records that do not appear in English searches.

**Heuristic scoring** — Each candidate accumulates positive points (regional exclusivity, BBS/FTP distribution, non-English documentation) and negative points (presence on Steam/GOG, multilingual Wikipedia, remasters). Those that do not pass the threshold go to the discarded list; they do not disappear.

**Node verification** — A result is `CONFIRMED` only if it appears in ≥2 independent root domains. Single-source results remain as `UNCONFIRMED`.

---

## Test results

Tests executed across 6 controlled scenarios with version V7.5-MINI.
Model used: DeepSeek-R1. Date: June 2026.

| ID | Genre | Region | Result | PRIMARY | BORDERLINE | Descartes | Observation |
| :- | :----- | :----- | :-------- | :------ | :--------- | :-------- | :---------- |
| A1 | JRPG | Japan | CASE B ✓ | 1 | 2 | 4 | Queries in Japanese retrieved PC-98 titles with no records in English. |
| A2 | Metroidvania | Brazil | CASE A ✓ | 0 | 0 | 0 | The genre did not exist in the region at the time. Anti-hallucination control successful. |
| A3 | Strategy | Poland | CASE B ✓ | 2 | 1 | 3 | DOS tactical games retrieved via scanned Polish magazines. |
| B1 | Graphic adventure | Spain | CASE B ✓ | 1 | 2 | 2 | Scarce local distribution; most titles had international reach. |
| B2 | Shoot 'em up | South Korea | CASE B ✓ | 2 | 1 | 2 | Shareware retrieved via Korean BBS. Queries in Hangul were key. |
| B3 | Roguelike | International | CASE B ✓ | 2 | 2 | 2 | Effective filtering of well-known titles (ADOM, NetHack). |

**5 out of 6 cases** returned verifiable results. Case A2 (CASE A) is a system success, not a failure: the prompt did not invent data where there was none.

---

## Model variants

| Variant | Profile | Optimization | Usage |
| :--- | :--- | :--- | :--- |
| **V7.5-MINI** | **Flagship Model** | High speed & lower token usage. | Massive scanning for quick findings. |
| **V7.5-S** | **Deep Research** | High-precision validation and granular scoring. Higher token consumption. | Deep investigative report. Experimental. |
---

## Repository structure

lostware/
│
├── README.md
├── LICENSE
├── CHANGELOG.md
│
├── prompts/
│   ├── lostware-v7.5-mini.md        ← current official version
│   └── archive/
│       ├── lostware-v5.8-mini.md
│       ├── lostware-v6.2.md
│       └── lostware-v7.5-S.md       ← experimental
│
├── tests/
│   ├── results-v7.5-mini.html
└── docs/
└── scoring-engine.md


---

## Known limitations

- The model may violate the genre purity rule in search round 2, expanding the term without indicating it. Round 2 results should be read with that bias in mind.
- Sources requiring manual access (magazine PDFs, private FTPs, BBS archives) are not accessible by the model. They appear as clues in `UNCONFIRMED`, not as verified results.
- Social media posts are not valid archaeological sources even if the model includes them. Manual verification is recommended for any result with that origin.
- If found, the model provides information and download links, but it has no way to validate them as safe. It is the user's responsibility to take precautions for malware protection. While keeping the system antivirus updated is usually enough, it is recommended to scan downloaded files with online tools like [Virus Total](https://www.virustotal.com/gui/home/upload).

---

## Project

Developed by **KRANK** as a research tool for the channel **[Intermosh](https://www.youtube.com/@intermosh)** — Video games, AI, experiments, and technology from the margins.

---

## License

MIT. See [LICENSE](LICENSE).
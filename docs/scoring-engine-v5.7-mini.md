# Scoring Engine 5.7 MINI

The scoring engine is the mechanism LOSTWARE uses to classify candidates found during search. It replaces subjective judgment ("does this feel obscure?") with a structured set of evidence-based rules applied consistently across every result.

---

## How it works

Each candidate title starts at a base score of 0. Evidence gathered during search adds or subtracts points. The final score determines whether the title appears in the output, and in which tier.

```
SCORE = SUM(positive indicators) - SUM(negative indicators)
```

The model applies this formula based only on evidence found during the current session. Prior knowledge is not evidence.

---

## Positive indicators

| Indicator | Points | Notes |
| :--- | :--- | :--- |
| Regional exclusive — never released outside home territory | +2 | Must be supported by absence of international distribution evidence |
| Documentation exclusively in a non-English language | +2 | Manuals, reviews, and forum posts all in native language only |
| Verified Prototype, Beta, or Cancelled status | +2 | Requires a primary source (developer statement, magazine announcement, archive file) |
| Distributed only via BBS, FTP dumps, or shareware compilations | +2 | No retail box, no commercial digital distribution |
| Mentioned only in a single regional magazine, local forum, or personal fansite | +1 | Single mention across all found sources |
| Listed in a preservation index or CD compilation with no standalone reviews | +1 | Presence in a list without individual coverage |

Maximum possible score: 10.

---

## Negative indicators

| Indicator | Points | Notes |
| :--- | :--- | :--- |
| Available on Steam, GOG, or any modern digital storefront | -3 | Any current availability on a commercial platform |
| Official remaster, remake, or modern re-release exists | -2 | Publisher-sanctioned re-release in any form |
| Wikipedia article in more than 2 languages | -2 | Indicates broad international documentation |
| Covered by major international gaming press at launch | -2 | Edge, PC Gamer, GameSpot, IGN, and equivalents |
| Published by a top-20 global publisher of the era | -1 | EA, Activision, LucasArts, Sierra, Microprose, and peers |

---

## Classification tiers

| Score | Tier | Output behavior |
| :--- | :--- | :--- |
| ≥ 5 | `[PRIMARY-HIGH]` | Included. Full 2-line context. |
| 3 – 4 | `[PRIMARY]` | Included. Full 2-line context. |
| 1 – 2 | `[BORDERLINE]` | Included. Tagged explicitly in context field. |
| ≤ 0 | `[MAINSTREAM]` | Excluded from primary list. Named in Discarded paragraph with justification. |

Titles sourced exclusively from AI-generated aggregator pages or SEO summary sites are dropped immediately regardless of score. These sources provide no verifiable evidence.

---

## Version differences

The scoring engine exists in two forms across LOSTWARE versions.

**V7.5-MINI** uses qualitative classification rather than numeric scoring. Instead of calculating a sum, the model evaluates the presence or absence of indicators and classifies directly:

- ≥ 3 positive, 0 negative → PRIMARY
- Mixed or unclear → BORDERLINE
- ≥ 2 negative outweighing positives → MAINSTREAM

This approach costs fewer reasoning tokens and produces consistent results for clear cases. It is less reliable for borderline candidates where indicator weights matter.

**V7.5-S Deep Research** uses the full numeric engine described above. Scores are calculated explicitly and appear in the output next to each title (`Score: [X]`). This makes scoring auditable but increases token cost per run.

---

## Calibration anchors

To anchor what "obscure" means in practice, both versions include reference titles. These are examples only — they are never valid search findings.

**Obscure reference (target range):** Planeta Vermelho, Father World, Cruel World, Ali Baba, Xiao-Ao-Jiang-Hu, Cyber Force, Biorobot's Base, B.I.G., Runen, Tears of Fury.

**Mainstream reference (exclusion range):** Another World, Flashback, Prince of Persia, Heart of Darkness, Fade to Black, Earth 2140, Rayman.

The calibration anchors define the vibe, not a blacklist. A title that resembles a mainstream anchor in genre or style is not automatically excluded — only its score determines its fate.

---

## Known limitations

**Score inflation on sparse evidence.** A title with no findable information can score +2 simply because its documentation is non-English and it appears only in one forum post. Absence of evidence is not the same as evidence of obscurity. The Discarded paragraph and manual verification step exist to catch these cases.

**Calibration leak risk.** The anchor titles appear in the system prompt. Models with strong recall may surface them as findings rather than references. If this occurs, results must be discarded. See the benchmark report for a documented instance of this failure in V7.5-S case A2.

**No partial credit.** Each indicator is binary — either the evidence supports it or it does not. A title distributed "mostly" via BBS but with one known retail copy does not earn the +2. The model is instructed to apply indicators conservatively.

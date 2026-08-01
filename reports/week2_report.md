# PSA Dataset — Preprocessing & Translation Summary

**Project:** PSA-NLP-Project (English / Kiswahili / Dholuo)
**Role:** P1 — Preprocessing Lead
**Status:** Complete — dataset cleaned, translated, and ready to hand off to modeling (P5)

## Source data

Raw combined dataset: 9,551 rows across 6 domains — Agriculture, Health, Education, Security, Governance, Civic Education.

Two raw exports existed with different `PSA_ID` schemes: one had a domain-batch ID collision bug, one didn't. The clean-ID version (`Combined_PSA_Raw.csv`) was used going forward.

## Data quality issues found

| Issue | Count | Resolution |
|---|---|---|
| Missing `PSA_ID` | 132 rows (all Security) | Generated placeholder IDs |
| Duplicate `PSA_ID` | 1,426 pairs / 2,852 rows | Re-keyed with traceable suffix (not dropped) |
| Corrupted cells (Excel error text) | 2 rows | Dropped |
| Duplicate English text | 11 rows | 10 dropped, kept first occurrence |
| Broken template blanks | 14 rows | Flagged, not dropped — needs source-text decision |
| Domain imbalance | Agriculture 57%, Civic Education <1% | Flagged, not resampled |
| Sparse Dholuo (original raw data) | 70% missing | Resolved via translation, see below |

Root cause of the ID collisions: two independent counters colliding with themselves, not one generic merge issue — Agriculture and Health shared one `PSA000XXX`-prefixed counter (1,169 of the pairs), while Education (internally) and Civic Education shared a separate bare-numeric counter (201 + 56 pairs). Spot-checked and confirmed as unrelated content in both cases, not a legitimate multi-sentence structure.

## Translation

Dholuo was translated using **NLLB-200** (`facebook/nllb-200-distilled-600M`, `eng_Latn` → `luo_Latn`), run on a Colab T4 GPU (fp16, batch size 32).

An initial approach scraping Google Translate's unofficial endpoint was rejected for two reasons: it violates Google's terms of service and risks rate-limiting at volume, and Google only added Dholuo support in July 2024 with acknowledged quality gaps for newly-added low-resource languages — unsuitable as ground-truth data for a project specifically about under-resourced-language transfer learning. NLLB-200 was already in the project's model scope, is open-source, and runs with no ToS risk.

Corrupted and duplicate-English rows were dropped **before** translating to avoid wasted translation effort.

## Preprocessing pipeline

- **Normalization** — whitespace collapsed, smart quotes converted to straight quotes. Apostrophes and numerals preserved (meaningful in Dholuo names and PSA dates/amounts).
- **Tokenization** — regex-based word tokenizer, keeps words with internal apostrophes/hyphens as single tokens.
- **Code-switching flagging** — heuristic (ALL-CAPS acronym detection + English function-word list), flagged not altered.
- **Cultural-terms glossary** — starter list: SHA, IEBC, WHO, ECHO, GIZ, county.

## Final dataset

`PSA_Processed_NLLB.Finalcsv` (full, with tokens + flags) and


- **9,539 rows × 14 columns**
- `PSA_ID`: unique and complete (0 duplicates, 0 missing)
- `Dholuo`: 100% coverage (9,539 / 9,539)
- Domain counts: Agriculture 5,401 · Health 1,659 · Education 1,021 · Security 994 · Governance 403 · Civic Education 61
- 14 rows flagged `has_template_gap`
- 1,903 Kiswahili rows and 3,065 Dholuo rows flagged for code-switching

## Known limitations / handoff notes

- Dholuo translations are machine-generated (NLLB-200), not human-translated. The Week 2 native-speaker validation subset is still needed before this can be treated as ground truth, not just cleaned text.
- Code-switch flags are heuristic, not a language-ID model — worth a spot check before relying on them for modeling decisions.
- The 14 template-gap rows still need a source-text recovery-vs-removal decision.
- The tokenizer and glossary are first-pass tools, meant to be extended.
- Re-keyed `PSA_ID`s (`id_was_rekeyed = True`) no longer match original source numbering exactly — cross-reference using the part of the string before the `_DUP` suffix.

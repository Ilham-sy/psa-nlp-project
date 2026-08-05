# PSA Multilingual Machine Translation — English / Kiswahili / Dholuo

Fine-tuning transformer models to translate Kenyan Public Service Announcements (PSAs) from English into Kiswahili and Dholuo, improving accessibility of health, safety, and civic information for communities underserved by general-purpose translation tools.

**Institution:** United States International University – Africa, Department of Data Science and Analytics, 2026
**Team:**
Harina Chohan · Susan Otieno · Ilham Mohamed · Kevin Korir · Yahya Mohamed

## Overview

General-purpose MT systems struggle with the domain-specific register and vocabulary of public communication, and Kiswahili and Dholuo — two of Kenya's most widely spoken local languages — are underserved as a result. This project builds a cleaned, parallel English–Kiswahili–Dholuo PSA corpus and fine-tunes NLLB-200 and mT5-small to close that gap.

## Dataset

- **9,539 cleaned records** across 6 domains: Agriculture, Health, Education, Security, Governance, Civic Education
- Parallel text in English, Kiswahili, and Dholuo
- Collected via a hybrid of manual collection and automated scraping
- Dholuo translations generated with NLLB-200 (not Google Translate — see *Translation approach* below)

| Domain | Rows |
|---|---|
| Agriculture | 5,401 |
| Health | 1,659 |
| Education | 1,021 |
| Security | 994 |
| Governance | 403 |
| Civic Education | 61 |

## Data pipeline

**Profiling** surfaced several issues in the raw combined data: 132 rows with missing `PSA_ID`, 1,426 duplicate-`PSA_ID` pairs (an ID-collision bug from merging domain batches with independent counters, not real duplicate content), 2 corrupted cells, 11 duplicate English sentences, 14 rows with broken template placeholders, sharp domain imbalance, and ~70% of rows missing Dholuo.

**Cleaning:** corrupted and duplicate-English rows dropped; colliding/missing `PSA_ID`s re-keyed with a traceable suffix rather than dropped; template gaps and code-switching flagged (not altered) for downstream review.

**Preprocessing:** whitespace/quote normalization (apostrophes and numerals preserved — meaningful in Dholuo names and PSA dates/amounts), regex-based tokenization, a starter cultural-terms glossary (SHA, IEBC, WHO, ECHO, GIZ, county).

**Translation approach:** Dholuo was generated with NLLB-200 (`facebook/nllb-200-distilled-600M`), run on a Colab T4 GPU. An initial approach scraping Google Translate's unofficial endpoint was rejected — it risks Google's rate-limiting/ToS enforcement at volume, and Google only added Dholuo support in July 2024 with acknowledged quality gaps, making it unsuitable as ground truth for a project specifically about under-resourced-language translation. NLLB-200 was already in scope as one of the two models being fine-tuned, is open-source, and carries no such risk.

## Models

| | mT5-small | NLLB-200 (distilled) |
|---|---|---|
| Checkpoint | `google/mt5-small` | `facebook/nllb-200-distilled-600M` |
| Architecture | Transformer, encoder–decoder | Transformer, encoder–decoder |
| BLEU (fine-tuned) | not formally scored — qualitative only | **80.51** |
| chrF (fine-tuned) | not formally scored — qualitative only | **89.55** |

Training: 3 epochs, batch size 4, max sequence length 128, AdamW optimizer, 80/10/10 train/dev/test split (7,610 / 951 / 952). mT5 was prompted with a prefix ("translate English to Kiswahili: …"); NLLB-200 used its native language-token conditioning (`eng_Latn` → `swh_Latn` / `luo_Latn`).

**Result:** NLLB-200 substantially outperformed mT5-small, consistent with its stronger pretrained coverage of African languages.

## Reproducing

1. Preprocessing: run `notebooks/PSA_Preprocessing_on_NLLB.ipynb` on an already-translated CSV (standard CPU runtime — no GPU needed).
2. Modeling: run `notebooks/English_Kiswahili_Translation_.ipynb` in Colab with a T4 GPU runtime.

## Limitations

- Model performs best on the Agriculture domain, which is 57% of the dataset — a direct consequence of domain imbalance, not evaluated correction.
- mT5-small output was repetitive/inaccurate outside agriculture-heavy phrasing; no formal BLEU/chrF was computed for it.
- Grammatical errors occur in longer sentences.
- Code-switching flags are heuristic (ALL-CAPS acronym + English stopword detection), not a language-ID model.
- Dholuo translations are machine-generated (NLLB-200), not human-translated — native-speaker validation is still needed before treating them as ground truth.
- The NLLB-200 BLEU score (80.51) is high relative to typical MT benchmarks; worth confirming there's no train/test leakage before presenting it as a general-purpose result, given how short and templated PSA text is.
- The poster's sample translation pair hasn't been confirmed against a saved model inference run.

## Future work

- Collect more PSA data across underrepresented domains
- Add more Kenyan and East African languages
- Conduct human evaluation with native speakers
- Deploy as a web-based translation utility for institutions

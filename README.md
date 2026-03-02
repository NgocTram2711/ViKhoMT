# ViKhoMT: Vietnamese–K'Ho Machine Translation Dataset

A professionally curated parallel corpus for Vietnamese–K'Ho Neural Machine Translation, focused on **community health communication**.

> **Paper:** *ViKhoMT: A Vietnamese–K'Ho Neural Machine Translation Dataset and Evaluation for Community Health Communication*
> Tram Truong, Vinh Nguyen, Dang Van Thin, Ngan Luu-Thuy Nguyen — University of Information Technology, VNU-HCM

---

## Overview

The K'Ho language is spoken by approximately **200,000 people** in the Central Highlands of Vietnam, yet remains severely underresourced in the digital domain. This dataset addresses a critical gap: the lack of high-quality, domain-specific parallel data for Vietnamese–K'Ho machine translation, particularly in the **healthcare sector** — where most materials exist only in Vietnamese.

**ViKhoMT** is the first publicly available, domain-specific parallel corpus for this language pair, containing **10,027 sentence pairs** compiled over a six-month period.

---

## Dataset

### File

| File | Format | Description |
|------|--------|-------------|
| `ViKhoMT.csv` | CSV | 10,027 Vietnamese–K'Ho parallel sentence pairs |

### Format

Each row contains two columns separated by a comma:

```
kho,vi
<K'Ho sentence>,<Vietnamese sentence>
```

**Example rows:**

| K'Ho | Vietnamese |
|------|------------|
| cau kòp là dùl nă oh ùr 9 sơnam | bệnh nhân là một bé gái 9 tuổi |
| kòp as sồt klồng măt tài bơsềt | bệnh viêm loét giác mạc do nấm |

### Statistics

| Split | Size |
|-------|------|
| Total | 10,027 sentence pairs |
| Train | 8,124 pairs (90% of non-test) |
| Validation | 903 pairs (10% of non-test) |
| Test | 1,000 pairs |

---

## Data Construction (Section 3)

The corpus was aggregated from **three primary sources**, each contributing complementary coverage of the community health domain.

### Source 1 — OCR-Extracted Data (~4,000 pairs)

Text was collected from the bilingual online newspaper **Báo ảnh Dân tộc và Miền núi**, a monthly publication that includes versions translated into 12 ethnic minority languages, including K'Ho. Articles from the *Health* and *Education* sections were selected. Text extraction was performed using **Google OCR**.

Because of the specific orthography of the K'Ho language, the raw OCR output contained significant noise. Common error types included:

| Error Type | OCR Output | Correct |
|---|---|---|
| Loss of diacritics | `kosot` | `kơsơt` |
| Incorrect diacritics | `gơwét` | `gơwèt` |
| Misinterpretation of special characters | `hở-tàng` | `hơ̆-tàng` |
| Confusion of similar characters | `kơnhuài` | `kơnhuàl` |

A rigorous **manual post-editing** process was conducted by language experts to correct these errors. All text was also normalized to **Unicode NFC** to resolve character inconsistencies.

### Source 2 — Manually Translated Data (~5,171 pairs from 200 articles)

To ensure high quality and domain expertise, **200 health articles** were professionally translated by a team of three experts:

- A member of the **VOV4 K'Ho language service** (Voice of Vietnam's ethnic minority broadcasting division) with 25 years of experience in K'Ho language preservation.
- Two **K'Ho language teachers** from schools in Di Linh, Lam Dong, who are also active translators for the Di Linh Parish.

The 200 articles cover **11 prevalent diseases** within the K'Ho community:

| Topic | % of Articles |
|-------|--------------|
| Dengue Fever | 12.0% |
| Diabetes | 9.8% |
| Measles | 9.3% |
| HIV | 7.5% |
| Stroke | 5.6% |
| Chickenpox | 9.2% |
| Hand, Foot, and Mouth Disease | 4.2% |
| Encephalitis | 5.1% |
| Rabies | 0.3% |
| Conjunctivitis | 5.6% |
| Diphtheria | 5.1% |
| Other health topics | 31.2% |

Any dialectal differences in vocabulary were standardized against the style guide of *Báo ảnh Dân tộc và Miền núi*. The translation workflow was twofold:

- **K'Ho → Vietnamese (100 articles):** Articles sourced from the official [VOV4 K'Ho website](https://vov4.vov.vn/kho) were translated into Vietnamese. The VOV4 expert performed the primary translation; the two teachers reviewed and validated.
- **Vietnamese → K'Ho (100 articles):** Vietnamese articles from reputable health outlets (Sức Khỏe Đời Sống, Báo Dân Tộc, VTV) were translated into K'Ho. Each teacher translated 50 articles; translations were cross-reviewed before a final validation by the VOV4 expert.

### Source 3 — Digitized Dictionary (856 pairs)

The corpus was enhanced with data from a digitized **1983 Vietnamese–K'Ho dictionary**, provided by the Lam Dong Department of Culture and Information and digitized by Nguyen Minh Thao of the Bao Lam Medical Center. The dictionary contains 7,065 words and 5,923 example sentences, from which **856 health-related example sentences** were extracted and filtered into the parallel corpus.

A critical preprocessing step for this source was **orthographic unification**: the 1983 dictionary followed a provincial simplification standard that mapped unique K'Ho characters to standard Vietnamese accented letters (e.g., `é` → `ě`, `ó` → `ŏ`, `í` → `ĭ`). This simplified standard is inconsistent with modern K'Ho publications. A reverse mapping was implemented to convert all dictionary data back to the **modern K'Ho orthography** used throughout the rest of the dataset.

---

## Data Preprocessing

All aggregated data underwent a multi-stage preprocessing pipeline:

1. **General cleaning:** Removed extraneous whitespace, residual HTML tags, and non-Latin characters from crawling/extraction.
2. **Sentence segmentation:** Segmented text using the period (`.`) as the primary delimiter.
3. **Abbreviation expansion:** Expanded common Vietnamese abbreviations (e.g., `bs` → `bác sĩ`, `vn` → `Việt Nam`, `tp` → `thành phố`).
4. **Deduplication:** Removed duplicate sentence pairs.
5. **OCR alignment:** For the newspaper data, paragraphs were manually aligned, followed by a Python script for one-to-one sentence alignment based on sentence length ratios and punctuation cues.
6. **Orthographic unification:** Converted 1983 dictionary data from the simplified orthography to modern K'Ho diacritics.
7. **Format:** Each clean parallel sentence is stored as a single row with K'Ho and Vietnamese text separated by a comma.

---

## Monolingual Corpora (for Back-Translation Research)

In addition to the parallel corpus, two monolingual datasets were used in the original paper's back-translation experiments:

| Corpus | Source | Size |
|--------|--------|------|
| Vietnamese (health domain) | Vietnamese Curated Text Dataset — Viettel Solutions | 30,000 sentences |
| K'Ho | Health section of [VOV4 news website](https://vov4.vov.vn/kho) | 23,000 sentences |

These are **not included** in this repository but are described here for reproducibility.

---

## Benchmark Results

Models were evaluated using **sacreBLEU**. The best-performing system (M2M100_418M with back-translation and filtering) achieves:

| Direction | BLEU Score |
|-----------|-----------|
| K'Ho → Vietnamese | **60.5** |
| Vietnamese → K'Ho | **56.4** |

Full results across all model architectures (Transformer-base, mBART-50, NLLB-200, SeamlessM4T, M2M100) are reported in the paper.

---

## License

This dataset is released for **free research purposes** to support future studies and the development of practical translation tools for the K'Ho community.

---

## Citation

If you use ViKhoMT in your research, please cite:

```bibtex
@inproceedings{truong2025vikhomt,
  title     = {ViKhoMT: A Vietnamese--K'Ho Neural Machine Translation Dataset and Evaluation for Community Health Communication},
  author    = {Truong, Tram and Nguyen, Vinh and Van Thin, Dang and Luu-Thuy Nguyen, Ngan},
  booktitle = {},
  year      = {2025},
  institution = {University of Information Technology, VNU-HCM}
}
```

---

## Acknowledgments

This research is funded by Vietnam National University, Ho Chi Minh City (VNU-HCM), under grant number **NCM2025-26-02**.

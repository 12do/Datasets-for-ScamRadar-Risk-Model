# DATA PROCESSED SUMMARY — SCAM RADAR

**Generated At:** 2026-11-25 18:19:13  
**Builder Script:** `scripts/build_processed_datasets.py` (`seed=42`, deterministic, `data_raw/` untouched)  
**Decisions Applied:** `DEC-010` (Option A1), P1 English kept native for multilingual `BAAI/bge-m3` baseline (Option A), Mishra `spam` dropped (Choice 1).

---

## 1. Automated Hygiene & Leakage Assertions (`Fact`)

- **Train ↔ Val overlap (normalized text):** **0** (`PASS`)
- **Train ↔ Internal Test overlap (normalized text):** **0** (`PASS`)
- **Val ↔ Internal Test overlap (normalized text):** **0** (`PASS`)
- **PII Regex Audit across ALL `data_processed/text/*.csv`:** **0** VN phone numbers, **0** 9–16 digit STK/CCCD sequences, **0** raw emails (`PASS`)
- **Invalid labels / empty texts:** **0** (`PASS`)

---

## 2. Text Datasets for Binary Risk % Model (`data_processed/text/`)

| File | Rows | Label `0` (Normal) | Label `1` (Scam) | % Scam | Language & Role |
|---|---:|---:|---:|---:|---|
| `train_vi_only.csv` | 1,911 | 1,383 | 528 | 27.6% | VI only (P0) — **Baseline 1 Train** |
| `val_vi.csv` | 382 | 277 | 105 | 27.5% | VI only (P0) — **Validation & Calibration** |
| `test_vi_internal.csv` | 254 | 184 | 70 | 27.6% | VI only (P0) — **Internal Test (Zero Leakage)** |
| `train_aux_vi_vispam.csv` | 1,500 | 1,500 | 0 | 0.0% | VI (P2 Train-only: 1,000 NO-SPAM + 500 promo) |
| `train_aux_vi_hoangvt.csv` | 6,672 | 4,700 | 1,972 | 29.6% | VI (P1 Train-only: `hoangvt2501/data_scam` localized + gendata dialogues) |
| `train_aux_en_p1.csv` | 6,930 | 5,632 | 1,298 | 18.7% | EN (P1 Train-only: Mishra no-spam + BothBosu) |
| `train_combined_ready.csv` | 17,013 | 13,215 | 3,798 | 22.3% | **Combined Train (use `source`/`is_primary_vi` for ablation & weighting)** |
| `train_aux_vi_targeted_v4.csv` | 552 | 150 | 402 | 72.8% | VI (V4 Train-only LLM targeted: 95 police, 99 bank/OTP, 51 gov/tax, 44 job_ctv, 43 shipper, 40 relative, 30 investment + 150 symmetric hard negs; deduped from 850 raw) |
| `train_aux_vi_targeted_v4_1.csv` | 380 | 220 | 160 | 42.1% | VI (V4.1 Train-only LLM OOD-targeted: 70 PSA police/gov/bank/UBCKNN, 55 e-wallet/bank tx, 55 PCCC/maintenance/booking, 40 telco/card + 45 SIM/bio, 35 booking deposit, 40 PCCC/APK/CCCD, 40 EVN/water-purifier; 0 dups, 0 PII, 0 overlap with val/test/V4/train_vi, 0 accent leaks, balanced `(TB)`/`[TB]`) |
| `hard_negatives_real_clean.csv` | 55 | 55 | 0 | 0.0% | VI (Team real hard negatives, 100% PII-scrubbed; note 1 row overlaps `val_vi.csv` — exclude that row if added to Train) |

### Cleaning Reconciliation (`Fact`)
- **P0 `p0_vietnamese_sms`:** 2,991 raw → dropped 444 duplicates (including 1 conflicting-label pair) → **2,547** clean unique SMS.
- **P1 `hoangvt2501/data_scam` (`localization/` + `gendata/`):** 6,672 raw → dropped 0 duplicates → **6,672** kept (`0`: 4,700, `1`: 1,972). *(License: `null` — experimental Train-only; do NOT redistribute raw files in submission ZIP.)*
- **P1 `Mishra & Soni`:** 5,971 raw → dropped **489** `spam` rows + 150 duplicates → **5,332** kept (`0`: 4,833, `1`: 499).
- **P1 `BothBosu`:** 1,600 raw → dropped 2 duplicates → **1,598** kept (`0`: 799, `1`: 799).
- **Combined Train source breakdown:** `p1_hoangvt_vi_localized`: 6,634, `p1_mishra_en_sms`: 5,332, `p0_vietnamese_sms`: 1,911, `p1_bothbosu_en_dialogue`: 1,598, `p2_vispam_vi_nospam`: 1,000, `p2_vispam_vi_promo`: 500, `p1_hoangvt_vi_gendata`: 38

---

## 3. URL Lookup Datasets (`data_processed/url_lookup/`)

- **`phishvn_urls_clean.csv`:** **36,261** unique URLs (from 53,116 raw after removing 16,855 rows in `url_duplicates.csv`) — `phishing`: 19,909, `benign`: 16,352.
- **`phishing_lookup_vn.csv`:** **20,450** phishing URL/domain lookup records:
  - `verified_core` (PhishVN gold/silver): **2,559**
  - `community_bronze` (PhishVN bronze): **17,350**
  - `community_active` (Phishing.Database strict `.vn` + VN brands, deduped vs PhishVN): **541**

---

## 4. Team Manual Data Templates (`data_processed/templates/`)

- `manual_test_template.csv` — for ~150 manually written Vietnamese test messages (no LLM).
- `hard_negatives_template.csv` — for ~50–100 real anonymized OTP / bank balance / delivery SMS.
- `simulated_campaigns_template.csv` — for ~100–200 controlled multi-report scam campaigns (`campaign_id`) to evaluate Unsupervised Clustering (Silhouette, B-cubed F1, ARI).

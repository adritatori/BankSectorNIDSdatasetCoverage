# Banking-Sector NIDS Dataset Coverage of MITRE ATT&CK Techniques

> Artifacts repository for the paper: **"Multi-LLM Consensus Framework for Evaluating Banking-Sector NIDS Dataset Coverage of MITRE ATT&CK Techniques"**

---

## Overview

This repository contains all primary artifacts produced by the three-stage evaluation pipeline described in the paper. The framework systematically assesses how well existing NIDS benchmark datasets cover adversary techniques most relevant to banking infrastructure, by:

1. Extracting banking-sector ATT&CK techniques from MITRE ATT&CK v18
2. Filtering techniques by passive NIDS observability under NIST SP 800-94 constraints
3. Mapping the detectable baseline against five benchmark datasets using a 4-model LLM consensus engine

**Key results at a glance:**

| Metric | Value |
|---|---|
| Banking-sector techniques extracted | 210 |
| NIDS-detectable baseline (YES + PARTIAL) | 68 (32.4%) |
| Best-performing dataset (UNSW-NB15) | 82.2% weighted coverage |
| Largest coverage gap (CIC-DDoS2019) | 89.9% blind spot |
| LLM consensus threshold | ≥ 3/4 models (75%) |
| Fleiss' Kappa (inter-model agreement) | 0.586 (Moderate) |

---

## Repository Structure

```
BankSectorNIDSdatasetCoverage/
│
├── README.md
│
├── stage1_threat_identification/
│   └── banking_techniques_llm_classifications.csv
│
├── stage2_detectability_classification/
│   ├── detectability_final_results.csv
│   └── disputed_techniques_expert_review.csv
│
├── stage3_dataset_mapping/
    ├── mapping_CTU-13.xlsx
    ├── mapping_UNSW-NB15.xlsx
    ├── mapping_CICIDS2017.xlsx
    ├── mapping_CIC-DDoS2019.xlsx
    └── mapping_UWF-ZeekData22.xlsx

```

---

## File Descriptions

### Stage 1 — Threat Identification

**`stage1_threat_identification/banking_techniques_llm_classifications.csv`**

Raw LLM outputs for all 210 banking-sector ATT&CK techniques extracted from MITRE ATT&CK v18 (accessed November 2025). Contains per-technique classifications and reasoning from all four models.

| Column | Description |
|---|---|
| `technique_id` | MITRE ATT&CK technique ID (e.g., T1071.001) |
| `name` | Technique name |
| `tactics_str` | Associated tactic(s) |
| `frequency_category` | High-Priority / Common / Medium / Rare |
| `total_score` | Composite priority score (0–100) |
| `entity_count` | Number of banking entities using this technique |
| `Claude / Grok / GPT / Gemini Classification` | Per-model YES / PARTIAL / NO label |
| `Claude / Grok / GPT / Gemini Reasoning` | Per-model reasoning (≤15 words) |
| `all_entities`, `groups`, `software` | Threat actors and malware families |

---

### Stage 2 — Detectability Classification

**`stage2_detectability_classification/detectability_final_results.csv`**

Final consensus classification for all 210 techniques after majority voting (≥3/4 LLMs) and expert adjudication. The 68 techniques labelled YES or PARTIAL form the NIDS-detectable baseline used in Stage 3.

| Column | Description |
|---|---|
| `Final_Classification` | YES / PARTIAL / NO |
| `Classification_Source` | Unanimous (4/4) / Consensus (3/4) / Expert Review |
| `NIDS_Detectable` | Detectable / Not Detectable |

**`stage2_detectability_classification/disputed_techniques_expert_review.csv`**

The 21 techniques (10.0%) that failed to reach consensus (2-2 LLM splits or 3-way disagreement), resolved through structured expert review applying conservative classification rules.

| Column | Description |
|---|---|
| `split_pattern` | The disagreement pattern (e.g., PARTIAL vs YES) |
| `Claude / Grok / GPT / Gemini` | Individual model votes |
| `Manual_Decision` | Expert-adjudicated final label |
| `Manual_Reasoning` | Justification for the decision |
| `Status` | VERIFIED for all resolved cases |

---

### Stage 3 — Dataset Mapping

**`stage3_dataset_mapping/mapping_<DATASET>.xlsx`** (one file per dataset)

Each file maps the 68 NIDS-detectable techniques against one benchmark dataset using the same 4-model LLM consensus approach. Files cover: CTU-13, UNSW-NB15, CICIDS2017, CIC-DDoS2019, and UWF-ZeekData22.

| Column | Description |
|---|---|
| `match_type` | Direct / Indirect / None (final consensus) |
| `mapped_dataset_label` | Matching attack label(s) in the dataset |
| `feature_evidence` | Network features supporting the mapping |
| `nist_justification` | NIST SP 800-94 basis for the mapping decision |
| `Claude / Grok / GPT / Gemini match_type` | Per-model mapping votes |
| `Claude / Grok / GPT / Gemini feature_evidence` | Per-model evidence |

**Coverage classification:**

| Label | Definition |
|---|---|
| **Direct** | Dataset explicitly contains traffic implementing the technique's core behavior |
| **Indirect** | Dataset contains related activity partially representing the technique |
| **None** | No dataset labels correspond to the technique |

---

### Figures

**`figures/dashboard_detectability_analysis.png`**

Summary dashboard showing final classification distribution, detectability by tactic, classification source breakdown, and summary statistics.

---

## Methodology Summary

### LLM Consensus Engine

Four state-of-the-art models were used independently for both Stage 2 and Stage 3:

| Model | Provider |
|---|---|
| Claude Opus 4.5 | Anthropic |
| GPT-5.2 | OpenAI |
| Gemini 3 Pro | Google |
| Grok 4.1 | xAI |

All models were accessed via standard consumer chat interfaces between November 2025 and January 2026. A **75% agreement threshold** (≥3/4 models) was required for consensus. Disputed cases were resolved through structured expert review with conservative classification defaults.

### NIST SP 800-94 Passive Sensor Constraints

All detectability classifications enforce three hard constraints:

| Constraint | Description |
|---|---|
| No TLS decryption | Encrypted payloads (HTTPS, SSH, VPN) are opaque; only metadata visible |
| No endpoint visibility | Process execution, filesystem, memory, and local auth are out of scope |
| Passive-only monitoring | Sensor observes traffic via SPAN ports/TAPs; no inline prevention |

### Priority Scoring

Each technique is scored 0–100 as:

```
Priority Score = Entity Usage [0–40] + Tactic Importance [0–40] + Group Diversity [0–20]
```

Weighted coverage uses this score to ensure high-priority techniques contribute proportionally to dataset utility metrics.

---

## ATT&CK Framework Version

All technique extractions used **MITRE ATT&CK Enterprise v18.1**, released October 28, 2025, accessed via [https://attack.mitre.org/](https://attack.mitre.org/) in November 2025.

---

## Citation

---

## License

Data and artifacts are released for research reproducibility. Please cite the paper if you use these artifacts.

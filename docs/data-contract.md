# Fulgor Data Contract v2

<!-- AUTO-GENERATED from packages/schemas — do not edit by hand. -->
<!-- Regenerate: uv run scripts/gen-data-contract.py -->
<!-- Schema version: 2 -->

This document defines the data contract for transaction extracts submitted to the Fulgor platform. It is the single source of truth for column definitions, types, formats, and limits.

v2 is a superset of the v1 pilot contract; v1 files validate under v2.

## Required Columns

Every extract must include these columns.

| Column | Paper variable | Type | Notes |
|--------|---------------|------|-------|
| `transaction_id` | — | string | Customer-generated opaque token; unique |
| `transaction_time` | determines Δ | ISO 8601 | Timezone required or declared-UTC |
| `issuer_id` | I | string | Opaque token; shrinkage and atlas grouping key |
| `authorized` | A | 0/1 or bool |  |
| `reported` | R | 0/1 or bool | Meaningful only where A=1 |
| `label_arrival_time` | determines M | ISO 8601, may be empty |  |
| `observed_label` | Ỹ | 0/1 or bool, may be empty | Read only where O=1 |

**Derived at validation (never supplied):** `available_maturity_days` (Δ), normalized `A`/`R`, `M = 1{label_arrival_time ≤ T}` (given A=R=1), `O = A·R·M`.

## Optional Feature Columns (X)

Include the production fraud score and every input to the authorization rule (authorization ignorability). These inputs are **required for a validated backtest**; without them the report is labeled *exploratory*.

| Column | Description |
|--------|-------------|
| `amount` | Transaction amount |
| `merchant_id` | Merchant identifier |
| `mcc` | Merchant category code |
| `country_pair` | Originating/destination country pair |
| `channel` | Transaction channel |
| `device_risk` | Device risk score |
| `account_age_days` | Account age in days |
| `prior_decline_count` | Number of prior declines |

Additional numeric or categorical feature columns are accepted as pass-through.

## Stage History and Phase-2 Columns

Forward-compatible optional columns accepted and validated from day one.

| Column | Used by | Source |
|--------|---------|--------|
| `w1_*` | Sequential STR (E5) | Post-authorization signals |
| `w2_*` | Sequential STR (E5) | Post-reporting signals |
| `reason_code` | Type classifier (E7) | Chargeback reason code |
| `representment_outcome` | Epsilon estimation (E6), Class 2 game, type classifier | Representment result |
| `dispute_filed_time` | Class 2 dispute model | Dispute filing timestamp |
| `dispute_outcome` | Class 2 game | Dispute resolution outcome |
| `charge_off_date` | Class 3 identification (atlas labeling) | Charge-off date |
| `acquirer_id` | Class 4 shrinkage/grouping | Acquirer identifier |
| `merchant_tenure_days` | Class 4 merchant panel (E7) | Merchant tenure in days |
| `merchant_monitoring_flag` | Class 4 merchant panel (E7) | Merchant monitoring flag |
| `scam_flag` | Class 5 identification (atlas labeling) | Scam indicator |
| `audit_label` | Direct epsilon estimation | Manually audited ground-truth label |
| `audit_sample_flag` | Direct epsilon estimation | Marks rows in the audit sample |

**v1 alias rule:** the unprefixed v1 names `three_ds_result` and `evidence_quality` are accepted and mapped to `w1_three_ds_result` / `w2_evidence_quality`.

## Per-Release Column Prerequisites

Validation reports which engine releases an extract can support.

- **E1–E4:** Required columns (B.1) only.
- **E5 (sequential STR):** At least one `w1_*` or `w2_*` column; without them collapses to E2.
- **E6 (dispute game):** `amount`, `dispute_filed_time`, and a dispute-resolution outcome (`dispute_outcome` or `representment_outcome`). Penalty policy is run configuration.
- **E7 Class 4:** `merchant_id` + `acquirer_id` plus merchant panel: `merchant_tenure_days` and `merchant_monitoring_flag`.

## Corrected-Label Export Columns

Columns present in the corrected-label export (the product output).

### Base columns (always present)

| Column | Type | Description |
|--------|------|-------------|
| `transaction_id` | string | Transaction identifier (join key to your input data) |
| `fold_id` | int | Cross-validation fold assignment |
| `e_hat` | float | Estimated authorization propensity |
| `r_hat` | float | Estimated reporting propensity |
| `p_hat` | float | Estimated maturity propensity |
| `q_hat` | float | Estimated total observation propensity (e * r * p) |
| `str_pseudo_outcome` | float | Unclipped STR pseudo-outcome |
| `fulgor_pseudo_label` | float | Corrected pseudo-label (clipped to [0, 1]) |
| `positivity_status` | string | Positivity classification: identified, near_zero, structural_zero, or extrapolation_only |

### Range-mode columns (present only under `sensitivity_range`)

| Column | Type | Description |
|--------|------|-------------|
| `fulgor_pseudo_label_eps_low` | float | Pseudo-label at low end of sensitivity range |
| `fulgor_pseudo_label_eps_high` | float | Pseudo-label at high end of sensitivity range |

## Formats and Limits

### Accepted formats

| Format | Specification |
|--------|--------------|
| CSV | UTF-8, RFC 4180 |
| Parquet | Apache Parquet |

### Validation limits

| Limit | Value |
|-------|-------|
| Maximum CSV file size | 500 MB |
| Maximum Parquet file size | 2 GB |
| Maximum rows | 10,000,000 |
| Maximum columns | 1,000 |

### Timestamp format

All timestamps must be ISO 8601 with timezone information (or declared-UTC).

### Binary columns

Accept `0`/`1` or `true`/`false`.

### Schema version

Every report and manifest carries `schema_version: 2`.

## Privacy Requirements

- **No PANs** (Primary Account Numbers)
- **No names**
- **No addresses**
- **No direct cardholder identifiers**

All identifiers (`transaction_id`, `issuer_id`, `merchant_id`, etc.) must be customer-generated opaque tokens.

Validation rejects columns matching PAN patterns as defense in depth.


<!-- fhulgor public contract export: generated copy for OSS sync. -->

# FHE Circuit Proof of Computation

This document defines the first non-production encrypted arithmetic proof for
`fhulgor`. The goal is to prove that packed integer lanes can support the
server-side arithmetic shape before the encoding profile and bundle format are
locked.

This is not the Fulgor encrypted label engine. It is a deliberately small
proof that a later harness can implement without choosing new inputs,
operation order, expected outputs, or pass/fail thresholds.

## Background

The proof follows the same constraints used by practical FHE arithmetic
circuits:

- compute over ciphertexts with addition and multiplication
- pack independent values into SIMD-style slots
- keep division, logarithms, branching, and encrypted comparisons out of the
  first exact-integer proof
- compare encrypted computation against a plaintext oracle after local
  decryption

The optional noise term in this proof is an integer circuit input. It is not a
differential privacy guarantee and does not change the privacy model.

## Development Profile

The first harness uses a development-only Microsoft SEAL BFV profile.

| Field | Value |
| --- | --- |
| Profile id | `bfv-dev-batch-10` |
| Scheme | BFV |
| Library | Microsoft SEAL |
| Polynomial modulus degree | `4096` |
| Coefficient modulus | `CoeffModulus::BFVDefault(4096)` |
| Plain modulus | `PlainModulus::Batching(4096, 20)` |
| Encoder | `BatchEncoder` |
| Fixed-point scale | none; exact integer scale `1` |
| Logical rows | `10` |
| Block shape | one block containing the 10 logical rows |
| Slot layout | rows in slots `0..9`; all remaining slots are zero |

The generated plaintext modulus, encoder slot count, initial noise budget,
final noise budget, ciphertext byte sizes, and runtime are measured by the
harness. This profile is not production-approved.

## Plaintext Fixture

The proof uses one synthetic block with an authorization gate and two small
integer feature columns.

| Row | `authorized` | `feature_0` | `feature_1` |
| --- | ---: | ---: | ---: |
| 0 | 1 | 3 | 10 |
| 1 | 0 | 5 | 7 |
| 2 | 1 | 2 | 4 |
| 3 | 1 | 8 | 1 |
| 4 | 0 | 6 | 3 |
| 5 | 1 | 1 | 9 |
| 6 | 0 | 4 | 2 |
| 7 | 1 | 7 | 5 |
| 8 | 1 | 0 | 6 |
| 9 | 0 | 9 | 8 |

Constants:

| Name | Value | Role |
| --- | ---: | --- |
| `w0` | 4 | Weight for `sum_0` |
| `w1` | 3 | Weight for `sum_1` |
| `noise` | 5 | Optional integer noise term |
| `threshold` | 190 | Plaintext threshold subtracted from score |

## Plaintext Oracle

The harness must reproduce this hand calculation exactly.

```text
authorized * feature_0:
  row 0: 1 * 3 = 3
  row 1: 0 * 5 = 0
  row 2: 1 * 2 = 2
  row 3: 1 * 8 = 8
  row 4: 0 * 6 = 0
  row 5: 1 * 1 = 1
  row 6: 0 * 4 = 0
  row 7: 1 * 7 = 7
  row 8: 1 * 0 = 0
  row 9: 0 * 9 = 0

sum_0 = 3 + 0 + 2 + 8 + 0 + 1 + 0 + 7 + 0 + 0
      = 21

authorized * feature_1:
  row 0: 1 * 10 = 10
  row 1: 0 * 7  = 0
  row 2: 1 * 4  = 4
  row 3: 1 * 1  = 1
  row 4: 0 * 3  = 0
  row 5: 1 * 9  = 9
  row 6: 0 * 2  = 0
  row 7: 1 * 5  = 5
  row 8: 1 * 6  = 6
  row 9: 0 * 8  = 0

sum_1 = 10 + 0 + 4 + 1 + 0 + 9 + 0 + 5 + 6 + 0
      = 35

score = w0 * sum_0 + w1 * sum_1 + noise
      = 4 * 21 + 3 * 35 + 5
      = 84 + 105 + 5
      = 194

margin = score - threshold
       = 194 - 190
       = 4
```

The expected decrypted lane `0` values are:

| Output | Expected value |
| --- | ---: |
| `sum_0` | 21 |
| `sum_1` | 35 |
| `score` | 194 |
| `margin` | 4 |

## Circuit Inputs

The harness encrypts packed vectors with values in slots `0..9` and zeros in
all remaining slots.

```text
ct_A     = Enc([1, 0, 1, 1, 0, 1, 0, 1, 1, 0, 0, ...])
ct_X0    = Enc([3, 5, 2, 8, 6, 1, 4, 7, 0, 9, 0, ...])
ct_X1    = Enc([10, 7, 4, 1, 3, 9, 2, 5, 6, 8, 0, ...])
ct_noise = Enc([5, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...])
```

Plaintext masks and constants are encoded with lane `0` set and all other
lanes zero unless stated otherwise.

```text
pt_w0        = [4, 0, 0, ...]
pt_w1        = [3, 0, 0, ...]
pt_threshold = [190, 0, 0, ...]
```

## Operation Order

The encrypted proof must run these operations in order:

```text
ct_gated_0 = ct_A * ct_X0
ct_gated_1 = ct_A * ct_X1

ct_sum_0 = reduce_first_n_slots(ct_gated_0, n = 10)
ct_sum_1 = reduce_first_n_slots(ct_gated_1, n = 10)

ct_score  = (pt_w0 * ct_sum_0) + (pt_w1 * ct_sum_1) + ct_noise
ct_margin = ct_score - pt_threshold
```

`reduce_first_n_slots` may use rotations, masks, or another prototype-only
strategy. Its contract for this proof is strict:

- decrypted lane `0` equals the sum of slots `0..9`
- the result does not depend on slots outside `0..9`
- all later checked values are read from lane `0`

The proof does not perform encrypted division, logarithms, clipping, or
threshold comparison. The comparison is the exact decrypted margin check above.

## Harness Output

The harness writes a machine-readable summary with actual run metadata.

```json
{
  "profile_id": "bfv-dev-batch-10",
  "scheme": "BFV",
  "poly_modulus_degree": 4096,
  "coeff_modulus": "CoeffModulus::BFVDefault(4096)",
  "plain_modulus": 0,
  "slot_count": 0,
  "logical_rows": 10,
  "fixed_point_scale": 1,
  "expected": {
    "sum_0": 21,
    "sum_1": 35,
    "score": 194,
    "margin": 4
  },
  "decrypted": {
    "sum_0": 21,
    "sum_1": 35,
    "score": 194,
    "margin": 4
  },
  "noise_budget_bits": {
    "initial": 0,
    "after_gated_products": 0,
    "final": 0
  },
  "ciphertext_bytes": {
    "ct_A": 0,
    "ct_X0": 0,
    "ct_X1": 0,
    "ct_noise": 0,
    "ct_sum_0": 0,
    "ct_sum_1": 0,
    "ct_score": 0,
    "ct_margin": 0
  },
  "runtime_ms": 0,
  "passed": true
}
```

`plain_modulus`, `slot_count`, `noise_budget_bits`, `ciphertext_bytes`, and
`runtime_ms` are placeholders in this document. The harness must fill them from
the actual SEAL run.

## Pass Criteria

The proof passes only when all of these are true:

- key generation succeeds for `bfv-dev-batch-10`
- all four input vectors encrypt successfully
- the encrypted multiply, add, and slot-reduction operations complete
- decrypted lane `0` equals `sum_0 = 21`, `sum_1 = 35`, `score = 194`, and
  `margin = 4`
- no checked arithmetic wraps modulo the generated plaintext modulus
- the run summary records actual profile, modulus, slot-count, noise-budget,
  ciphertext-size, and runtime metadata

## Failure Criteria

The proof fails if any of these occur:

- any decrypted value differs from the plaintext oracle
- reduction depends on non-zero or unspecified slots outside `0..9`
- any operation wraps under the plaintext modulus
- the final noise budget is exhausted or too small to support one additional
  ciphertext multiplication in a slightly larger follow-up circuit
- ciphertext size, runtime, or slot layout makes the next bundle-to-circuit
  spike obviously impractical
- the harness uses CKKS or approximate tolerances for this BFV exact-integer
  proof

## Follow-Up Decision Inputs

The harness closeout must record:

- whether BFV packed integer lanes are viable for the first encoding profile
- whether the row-slot layout should remain rows-in-slots or move to another
  packing shape
- whether the generated plaintext modulus safely exceeds the toy arithmetic
  range
- the multiplicative depth consumed by `authorized * feature_j`, slot
  reduction, and weighted aggregation
- the decryption rounding, client-side thresholding, plaintext modulus, and
  scale assumptions needed for future row-level margin circuits
- whether BFV/BGV remains sufficient for the selected first circuit target or a
  non-production CKKS preview profile is required later

# fhulgor Source Map

This map records the public contract files imported from Fulgor platform
exports. It is the traceability layer for validation parity work before
fhulgor adds runtime crates.

The platform export package is rooted at `generated/fhulgor-contract/` in the
platform repository. Imported file hashes come from
[fhulgor-contract-manifest.json](../fhulgor-contract-manifest.json). Hashes are
SHA-256.

## Source Order

1. Exported schema and validation fixtures in this repository.
2. Platform source hashes recorded by the export manifest.
3. Public data-contract documentation.
4. fhulgor bundle, result, encoding, and FHE protocol docs as they are added.

## Upload And Validation Path

The current Fulgor upload path computes a SHA-256 digest on the client, requests
an upload URL, uploads the file through the presigned URL, confirms the upload,
then calls dataset validation. The API checks accepted file extensions and size
limits before upload bytes are accepted. Validation uses a timezone-aware
`train_end`, bounded download handling, CSV or Parquet parsing, v1 alias
normalization, `ValidationInputMetadata`, and the platform validator report
builder.

These upstream paths describe that behavior. They are behavioral references,
not imported public files in the current export package.

| Platform source path | Behavior referenced by fhulgor |
| --- | --- |
| `apps/web/src/api/upload.ts` | Client SHA-256 digest, upload URL request, presigned upload, upload confirmation, validation request |
| `apps/api/src/api/datasets.py` | Accepted file extensions and size checks before upload bytes are sent |
| `apps/api/src/api/validation.py` | `train_end` parsing, bounded download, CSV/Parquet parse, v1 aliases, input metadata, validator call |

## Platform Source Hashes

These files produced or defined the exported contract package.

| Platform source path | Manifest source hash | Export responsibility |
| --- | --- | --- |
| `packages/engine/src/engine/gates.py` | `e42ef888338b23646fe5935d5c0cc0b3efb75f0325e5475d121c84ed94ee4d31` | Validation prerequisite verdicts and guidance gates |
| `packages/engine/src/engine/validator.py` | `26d62d14eb0bd78f346ba1cc12ab0408bc956c65d1b844f9175db211024c455f` | Validation report semantics for exported parity fixtures |
| `packages/schemas/src/schemas/artifacts.py` | `0be2f0ae4b41d76d1599be29ea7561511f8384a4ca61d457361f3ff38223fd2e` | `ValidationReport` and related schema-owned artifact shapes |
| `packages/schemas/src/schemas/columns.py` | `c15669b1ed727785be45bd5fbe2492ff3b9f35a8fef9177b69f580ae0e72fb99` | Required columns, optional columns, derived columns, aliases, and denylist |
| `packages/schemas/src/schemas/contract.py` | `b9e77e185758531a3eb4f605d93096a421b29811d77b0afab4cb50c682cef085` | Validation row, column, and byte limits |
| `packages/schemas/src/schemas/readers.py` | `d91d067ab1e027eeed0fc0056672507fed64955cf49c653f818eeead06f81d12` | CSV and Parquet reader contract used by validation ingestion |
| `packages/schemas/src/schemas/serialization.py` | `85151e6f8618318ef0d36743af26642fbb2cff822c2a2d3dd8e0a51bef57aaf6` | Deterministic JSON serialization for expected reports and manifests |
| `packages/schemas/src/schemas/version.py` | `90b7cb162654f70a2ca7c401ac40fb28f890bbef3869d87deb830fd61a81880b` | Contract schema version |
| `scripts/export-fhulgor-contract.py` | `375fc18f261eb3279baced1379ba0096dcab87902145d6b960a5ee200bbae914` | Contract export assembly |
| `scripts/gen-data-contract.py` | `31d75ac62a515c086a743e6966325828aa59cdaabbd8844b5ae758013b2dae78` | Public data-contract document generation |

## Imported Contract Files

Every file below is copied from `generated/fhulgor-contract/<path>` in the
platform repository.

| Public file | Platform export source path | Generated hash | fhulgor consumer |
| --- | --- | --- | --- |
| [docs/data-contract.md](data-contract.md) | `generated/fhulgor-contract/docs/data-contract.md` | `e4f55e6b0384c023f8c6d7e36826287882de89b7113ff620d13585b478372a02` | Public contract reference and validation parity documentation |
| [schemas/validation-report.schema.json](../schemas/validation-report.schema.json) | `generated/fhulgor-contract/schemas/validation-report.schema.json` | `04d4915da847dfb615f03ffdcc5bc374da2e33e573a40ce114b1e7c92ca68e06` | `fhulgor-core` report model and schema compatibility tests |
| [schemas/columns.json](../schemas/columns.json) | `generated/fhulgor-contract/schemas/columns.json` | `ba7b5a188d0dbd67991ba6956a2fec153325dfa1405b41ec7b49713e9551708b` | `fhulgor-core` contract import, validation, and encoding-profile inputs |
| [schemas/v1-column-aliases.json](../schemas/v1-column-aliases.json) | `generated/fhulgor-contract/schemas/v1-column-aliases.json` | `2a6d8d7c502afeba9109ab72443ca291c6da2e092e264cb90d781947474614a9` | `fhulgor-core` v1 alias normalization tests |
| [schemas/validation-limits.json](../schemas/validation-limits.json) | `generated/fhulgor-contract/schemas/validation-limits.json` | `a79ce891df57c4863275216307bce147defa7363115b5e6107d78b6bf2118e8e` | `fhulgor-core` file limit and metadata validation tests |
| [fixtures/platform-validation/manifest.json](../fixtures/platform-validation/manifest.json) | `generated/fhulgor-contract/fixtures/platform-validation/manifest.json` | `0d0b4d73c3e708557c5b3c82581346ecb7c85e86631a487bf3bf8a5e18dcafc0` | `fhulgor-core` golden fixture loader |
| [fixtures/platform-validation/inputs/clean_csv.csv](../fixtures/platform-validation/inputs/clean_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/clean_csv.csv` | `2677c1956e15e094ef440c61cff5aa85f4e919dae1e6a8e8281fba2903b092c9` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/clean_csv.json](../fixtures/platform-validation/expected-reports/clean_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/clean_csv.json` | `bf0df32707fe9443bf4eab7ab01c675cb7893e96469ecd0bb2252ce534630aa0` | Validation parity expected report |
| [fixtures/platform-validation/inputs/clean_parquet.parquet](../fixtures/platform-validation/inputs/clean_parquet.parquet) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/clean_parquet.parquet` | `66e2500db79694526a5c863f2a74cba450e044f8868f741aa8f3000315610e23` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/clean_parquet.json](../fixtures/platform-validation/expected-reports/clean_parquet.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/clean_parquet.json` | `bf0df32707fe9443bf4eab7ab01c675cb7893e96469ecd0bb2252ce534630aa0` | Validation parity expected report |
| [fixtures/platform-validation/inputs/missing_required_column_csv.csv](../fixtures/platform-validation/inputs/missing_required_column_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/missing_required_column_csv.csv` | `04339663a58e4bb9046e44b1cbabf376b46a8f0f64e75a06eadcdb4e8c479377` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/missing_required_column_csv.json](../fixtures/platform-validation/expected-reports/missing_required_column_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/missing_required_column_csv.json` | `5f129f8883ec08767c790e022b8143d6e18e0fb116731b71268ffce742b31d92` | Validation parity expected report |
| [fixtures/platform-validation/inputs/invalid_binary_csv.csv](../fixtures/platform-validation/inputs/invalid_binary_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/invalid_binary_csv.csv` | `47d0c60c3c199b8f30d8d86a59dd8b0289801d6e735206d865be65a30670ee32` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/invalid_binary_csv.json](../fixtures/platform-validation/expected-reports/invalid_binary_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/invalid_binary_csv.json` | `f2f3c4a967636d1849940e91c20fb4b3a154b176edf3eef144ca9cc8e6634de9` | Validation parity expected report |
| [fixtures/platform-validation/inputs/invalid_observed_label_csv.csv](../fixtures/platform-validation/inputs/invalid_observed_label_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/invalid_observed_label_csv.csv` | `1113f24ada52507f7a68638b587e56b547b6ff2dd4d20ed4515237a629453b9d` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/invalid_observed_label_csv.json](../fixtures/platform-validation/expected-reports/invalid_observed_label_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/invalid_observed_label_csv.json` | `c0a61306883e6de26fdc2a9cbcf9f42967a2486c23301aa88fe62cca02178074` | Validation parity expected report |
| [fixtures/platform-validation/inputs/invalid_string_csv.csv](../fixtures/platform-validation/inputs/invalid_string_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/invalid_string_csv.csv` | `f1fd9bb940b799347cb554e497fda2dadc1e3ed0f7d12bf59412d6a158a835bb` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/invalid_string_csv.json](../fixtures/platform-validation/expected-reports/invalid_string_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/invalid_string_csv.json` | `0ae8bb3a6b5cfb1cc1b0dfa9bc5626cffcac5f4572f94683f605ca8cf241a34b` | Validation parity expected report |
| [fixtures/platform-validation/inputs/invalid_timestamp_csv.csv](../fixtures/platform-validation/inputs/invalid_timestamp_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/invalid_timestamp_csv.csv` | `ceaea2ec982ca538b4cd151578bf385c7f6a10bbd75cee1e650b3381a41313e1` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/invalid_timestamp_csv.json](../fixtures/platform-validation/expected-reports/invalid_timestamp_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/invalid_timestamp_csv.json` | `1eb2ebeb8926860803b2d0fa0ed78ec55dfa970477ca5097cb51c071c3af5d82` | Validation parity expected report |
| [fixtures/platform-validation/inputs/pan_pattern_csv.csv](../fixtures/platform-validation/inputs/pan_pattern_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/pan_pattern_csv.csv` | `950a676c5eabd58a8058ad56ac876efa3807e8738adb1d3218c0a0a267bf0cc5` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/pan_pattern_csv.json](../fixtures/platform-validation/expected-reports/pan_pattern_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/pan_pattern_csv.json` | `de51fd2e6505abbf5769d134b8c5917cb98b2cba7c4c9841ca94d9be661f6f9f` | Validation parity expected report |
| [fixtures/platform-validation/inputs/v1_aliases_csv.csv](../fixtures/platform-validation/inputs/v1_aliases_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/v1_aliases_csv.csv` | `a42015fa38c06e3b4c94e95a653e73c53d6d29a8917861796b644023aaa66382` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/v1_aliases_csv.json](../fixtures/platform-validation/expected-reports/v1_aliases_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/v1_aliases_csv.json` | `13c905c4701a6f90714650258d6e5fdfd245bc4a78c9bf189ef5952a24ce17bb` | Validation parity expected report |
| [fixtures/platform-validation/inputs/v1_aliases_parquet.parquet](../fixtures/platform-validation/inputs/v1_aliases_parquet.parquet) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/v1_aliases_parquet.parquet` | `4c7c3cda98316419559f15ac1bb0e074ef340969cfee4aecd764c21a3e96f3d4` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/v1_aliases_parquet.json](../fixtures/platform-validation/expected-reports/v1_aliases_parquet.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/v1_aliases_parquet.json` | `13c905c4701a6f90714650258d6e5fdfd245bc4a78c9bf189ef5952a24ce17bb` | Validation parity expected report |
| [fixtures/platform-validation/inputs/after_train_end_csv.csv](../fixtures/platform-validation/inputs/after_train_end_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/after_train_end_csv.csv` | `1aeb75cdf08491af87a95fea6a09b0d5f40e7dfadabae93fdbdb73d6a9c26a90` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/after_train_end_csv.json](../fixtures/platform-validation/expected-reports/after_train_end_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/after_train_end_csv.json` | `593c74308c1844a4c488f6735fec47be2a461300d6c133bf8073e38e35d931e9` | Validation parity expected report |
| [fixtures/platform-validation/inputs/non_monotone_csv.csv](../fixtures/platform-validation/inputs/non_monotone_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/non_monotone_csv.csv` | `6dce31bd105ede8430c4910c12f86d177a0f8158a6424c8a653cbaf966b93025` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/non_monotone_csv.json](../fixtures/platform-validation/expected-reports/non_monotone_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/non_monotone_csv.json` | `bab93a3ad4392ec60d2e8995db45b28095f3927761826c7d40a65e74b98edcac` | Validation parity expected report |
| [fixtures/platform-validation/inputs/row_limit_metadata_csv.csv](../fixtures/platform-validation/inputs/row_limit_metadata_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/row_limit_metadata_csv.csv` | `2677c1956e15e094ef440c61cff5aa85f4e919dae1e6a8e8281fba2903b092c9` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/row_limit_metadata_csv.json](../fixtures/platform-validation/expected-reports/row_limit_metadata_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/row_limit_metadata_csv.json` | `0a9eb2cfe8e494c4d70283b90c5bf9da36fdfd66fbc4cd66a9ce55e3917f08c9` | Validation parity expected report |
| [fixtures/platform-validation/inputs/column_limit_metadata_csv.csv](../fixtures/platform-validation/inputs/column_limit_metadata_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/column_limit_metadata_csv.csv` | `2677c1956e15e094ef440c61cff5aa85f4e919dae1e6a8e8281fba2903b092c9` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/column_limit_metadata_csv.json](../fixtures/platform-validation/expected-reports/column_limit_metadata_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/column_limit_metadata_csv.json` | `9c2411046e9db1e61aae32a3a794b7874757037aa2f1263d1f6ae4a890bcb0b6` | Validation parity expected report |
| [fixtures/platform-validation/inputs/file_size_limit_metadata_csv.csv](../fixtures/platform-validation/inputs/file_size_limit_metadata_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/file_size_limit_metadata_csv.csv` | `2677c1956e15e094ef440c61cff5aa85f4e919dae1e6a8e8281fba2903b092c9` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/file_size_limit_metadata_csv.json](../fixtures/platform-validation/expected-reports/file_size_limit_metadata_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/file_size_limit_metadata_csv.json` | `257fe984283f0fb814580a21ba7a3283b4e588fb3b773ff85177e051d5514f34` | Validation parity expected report |
| [fixtures/platform-validation/inputs/maturity_inadequate_csv.csv](../fixtures/platform-validation/inputs/maturity_inadequate_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/maturity_inadequate_csv.csv` | `d7e0e82fa594aa56cbe0c16c0fdf361635a0fc8879285b43d8e8d1367a11f955` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/maturity_inadequate_csv.json](../fixtures/platform-validation/expected-reports/maturity_inadequate_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/maturity_inadequate_csv.json` | `85f8058de7b98b8fe73a7e10236dd41f51e35abdc5d384826a41df484d8907c1` | Validation parity expected report |
| [fixtures/platform-validation/inputs/vintage_inadequate_csv.csv](../fixtures/platform-validation/inputs/vintage_inadequate_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/vintage_inadequate_csv.csv` | `0e106891ec52dbc32595f8adc6d131af8e4002a71009d692d493bc7cc09df28e` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/vintage_inadequate_csv.json](../fixtures/platform-validation/expected-reports/vintage_inadequate_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/vintage_inadequate_csv.json` | `fa440e78d672116fccaae9ecd1954a2136b4719ef66eda47f86000a00624c1f9` | Validation parity expected report |
| [fixtures/platform-validation/inputs/sufficiency_below_guidance_csv.csv](../fixtures/platform-validation/inputs/sufficiency_below_guidance_csv.csv) | `generated/fhulgor-contract/fixtures/platform-validation/inputs/sufficiency_below_guidance_csv.csv` | `2677c1956e15e094ef440c61cff5aa85f4e919dae1e6a8e8281fba2903b092c9` | Validation parity fixture input |
| [fixtures/platform-validation/expected-reports/sufficiency_below_guidance_csv.json](../fixtures/platform-validation/expected-reports/sufficiency_below_guidance_csv.json) | `generated/fhulgor-contract/fixtures/platform-validation/expected-reports/sufficiency_below_guidance_csv.json` | `86a4343335c49eb8cd544fe8b006cc73feb2ec2b4d5ee9df0057f00cc0101c62` | Validation parity expected report |

## Validation Behavior Coverage

The public fixture manifest maps each input to its expected platform
`ValidationReport`, `train_end`, input metadata, issue class, and validity.

| Behavior | Fixture cases | Source of expected behavior |
| --- | --- | --- |
| Valid CSV and Parquet files | `clean_csv`, `clean_parquet` | `packages/engine/src/engine/validator.py`; expected reports in `fixtures/platform-validation/expected-reports/` |
| Missing required column | `missing_required_column_csv` | `packages/schemas/src/schemas/columns.py`; `packages/engine/src/engine/validator.py` |
| Invalid binary or observed-label values | `invalid_binary_csv`, `invalid_observed_label_csv` | `packages/engine/src/engine/validator.py` |
| Invalid string field | `invalid_string_csv` | `packages/engine/src/engine/validator.py` |
| Invalid timestamp | `invalid_timestamp_csv` | `packages/schemas/src/schemas/readers.py`; `packages/engine/src/engine/validator.py` |
| PAN-like value rejection | `pan_pattern_csv` | `packages/engine/src/engine/validator.py` |
| v1 alias normalization | `v1_aliases_csv`, `v1_aliases_parquet` | `packages/schemas/src/schemas/columns.py`; `schemas/v1-column-aliases.json` |
| Rows after `train_end` | `after_train_end_csv` | `packages/engine/src/engine/validator.py` |
| Non-monotone row warnings | `non_monotone_csv` | `packages/engine/src/engine/validator.py` |
| Row, column, and byte limit metadata | `row_limit_metadata_csv`, `column_limit_metadata_csv`, `file_size_limit_metadata_csv` | `packages/schemas/src/schemas/contract.py`; `schemas/validation-limits.json` |
| Maturity, vintage, and sufficiency guidance | `maturity_inadequate_csv`, `vintage_inadequate_csv`, `sufficiency_below_guidance_csv` | `packages/engine/src/engine/gates.py`; `packages/engine/src/engine/validator.py` |

## Refresh Contract

To refresh this map, regenerate the platform export package, copy the public
export files into this repository, and update this document from the committed
manifest. Do not hand-edit schema literals or expected reports.

# fhulgor

`fhulgor` is the open-source client for preparing encrypted FHE bundles for Fulgor. The repository is in public bootstrap: it contains the exported validation contract, public fixture corpus, and roadmap needed before the Rust workspace and terminal app are added.

## Status

Current scope: repository skeleton and public contract landing zone. Runtime crates, command behavior, the Ratatui interface, and the Microsoft SEAL bridge are planned follow-up milestones.

## Locked Pre-M0 Decisions

- Public repository: `fulgorfi/fhulgor`.
- Project name, repository name, binary name, crate prefix, and bundle names stay `fhulgor`.
- Microsoft SEAL integration starts sidecar-first with narrow local file-based inputs and outputs. Sidecar scratch state stays outside upload bundles.
- Encoding profiles must classify every exported column or accepted column pattern as `encoded`, `derived`, `local_only`, or `unsupported`. Unknown transforms remain unsupported rather than guessed.
- The upload package is deterministic `bundle.fhulgor.tar`, containing exactly one `bundle.fhulgor/` root and sent through one presigned URL.
- Demo mode uses a dedicated public fixture at `fixtures/demo/demo_fixture.csv`; validation parity fixtures stay under `fixtures/platform-validation/`.

## v1 Claim Boundary

`fhulgor` v1 prepares encrypted upload bundles. It does not claim label reconstruction, encrypted label improvement, or production Fulgor-hosted FHE compute. Customers keep decryption keys and row mapping data local by default; Fulgor receives only upload-safe bundle data.

## Public Contract Exports

- `fhulgor-contract-manifest.json`
- `schemas/validation-report.schema.json`
- `schemas/columns.json`
- `schemas/v1-column-aliases.json`
- `schemas/validation-limits.json`
- `fixtures/platform-validation/`
- `docs/data-contract.md`

## Roadmap

See `docs/roadmap.md`.

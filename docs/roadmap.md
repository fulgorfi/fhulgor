# fhulgor Roadmap

This roadmap is evidence-gated. Public claims move only when the repository contains working artifacts that support them.

## Current: Public Bootstrap

- Repository skeleton for docs, schemas, fixtures, and future CI.
- Exported validation contract manifest.
- Public validation fixture corpus.
- Public data contract reference.

## Milestones

| Gate | Public Beat | Evidence | Claim Boundary |
| --- | --- | --- | --- |
| M0 | OSS skeleton | License, CI matrix, command help, and terminal shell snapshot | No validation parity or crypto yet |
| M1 | Validation parity | Golden fixture parity, validation CLI output, redacted terminal validation view, CI link | Local validation parity only; no encrypted bundle yet |
| M3 | FHE working locally | SEAL bridge boundary, key metadata, known-value encryption roundtrip, `roundtrip-test` output | Local crypto proof only; no server-side label engine |
| M5 | End-to-end roundtrip | Validate to keygen to encode to encrypt to inspect to synthetic decrypt-result transcript, plus demo-mode recording | Synthetic result fixture only; no Fulgor-hosted encrypted label improvement |
| C1 | Bundle-to-circuit spike | Real `bundle.fhulgor/` consumed by a server-side SEAL toy circuit, encrypted aggregate result, local decrypt proof | Compatibility proof only; not production corrected-label logic |

## Near-Term Work

1. Publish the source map that ties each public contract artifact to the exported validation behavior it represents.
2. Complete the toy encrypted arithmetic spikes before locking encoding profile details.
3. Add the Cargo workspace, CLI entrypoint, and Ratatui shell after the source map and protocol docs are pinned.
4. Keep customer-facing privacy claims aligned with shipped evidence.

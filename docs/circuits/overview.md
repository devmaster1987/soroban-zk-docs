---
sidebar_position: 1
---

# Circuits Overview

The Soroban ZK Toolkit includes four production-ready Circom circuits, each compiled to WebAssembly for browser and Node.js proof generation.

## Circuit list

| Circuit | File | Public inputs | Private inputs | Constraints |
|---------|------|---------------|----------------|-------------|
| `identity` | `identity.circom` | `commitment` | `secret`, `nullifierSeed` | ~1,200 |
| `membership` | `membership.circom` | `root`, `nullifier` | `leaf`, `pathElements[]`, `pathIndices[]` | ~8,000 |
| `range-proof` | `range.circom` | `commitment`, `min`, `max` | `value`, `blinding` | ~2,500 |
| `transfer` | `transfer.circom` | `senderNullifier`, `recipientCommitment`, `amount` | `senderSecret`, `senderBalance` | ~5,000 |

## Common patterns

All circuits share these conventions:

- **Poseidon hash** is used throughout for ZK-friendliness.
- **Nullifiers** prevent double-spending / double-proving without linking proofs to identities.
- **Commitments** are Pedersen-style hides: `commit(value, blinding) = Poseidon(value, blinding)`.

## Artifact files

Each circuit ships with:

| File | Description |
|------|-------------|
| `<circuit>.wasm` | WebAssembly witness generator |
| `<circuit>.zkey` | Proving key (from trusted setup) |
| `<circuit>.vk.json` | Verification key (register on-chain) |
| `<circuit>.r1cs` | R1CS constraint system (for audits) |

## See also

- [Identity circuit](./identity.md)
- [Membership circuit](./membership.md)

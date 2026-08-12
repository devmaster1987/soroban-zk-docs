---
sidebar_position: 1
---

# Zero-Knowledge Proofs

Zero-knowledge proofs (ZKPs) let one party (the **prover**) convince another party (the **verifier**) that a statement is true — without revealing *why* it is true or any underlying private data.

## The core idea

Imagine proving you know a password without ever sending the password itself. With a ZKP you produce a short **proof** that a verifier can check in milliseconds. The proof reveals nothing about the secret.

Three properties define a ZKP:

| Property | Meaning |
|----------|---------|
| **Completeness** | An honest prover with a valid witness will always convince the verifier |
| **Soundness** | A dishonest prover cannot fake a valid proof (except with negligible probability) |
| **Zero-knowledge** | The verifier learns nothing beyond the truth of the statement |

## ZK-SNARKs

The Soroban ZK Toolkit uses **ZK-SNARKs** (Succinct Non-interactive Arguments of Knowledge). "Succinct" means the proof is tiny (a few hundred bytes) and fast to verify — crucial for on-chain verification where every byte costs gas.

### How SNARKs work at a high level

1. A **circuit** encodes the computation you want to prove (e.g., "I know a preimage of this hash").
2. A **trusted setup** converts the circuit into proving and verification keys.
3. The **prover** supplies private inputs (the *witness*) and public inputs, runs the circuit, and outputs a proof.
4. The **verifier** checks the proof against the public inputs and the verification key.

On Soroban, step 4 happens inside a smart contract.

## Why ZKPs on Stellar?

Stellar's Soroban VM is deterministic and metered, making it an ideal environment for verifying cryptographic proofs. Proof verification is a fixed-cost computation with no loops or branching on secret data, so gas costs are predictable.

## Further reading

- [Groth16 explained](./groth16.md)
- [Merkle trees and membership proofs](./merkle-trees.md)

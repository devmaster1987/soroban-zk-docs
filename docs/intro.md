---
sidebar_position: 1
slug: /
---

# Soroban ZK Toolkit

The **Soroban ZK Toolkit** is a zero-knowledge proof infrastructure stack for the [Stellar](https://stellar.org) network. It allows Soroban smart contract developers to integrate privacy-preserving computations into their applications — without needing a deep background in cryptography.

## What is included

| Component | Description |
|-----------|-------------|
| **Circom circuits** | Four production-ready circuits: identity, membership, range proof, and transfer |
| **Groth16 verifier contract** | An on-chain Soroban contract that verifies Groth16 proofs |
| **soroban-zk-sdk** | TypeScript SDK for proof generation, witness computation, and contract interaction |
| **Trusted setup artifacts** | Pre-generated `.zkey` and verification key files for each circuit |

## Use cases

- **Identity verification** — prove you own a credential without revealing it
- **Private balances** — hide token amounts while proving solvency via range proofs
- **Allowlist / blocklist membership** — prove set membership without exposing the set
- **Confidential transfers** — transfer assets with hidden amounts

## Quick links

- [Installation](./getting-started/installation.md)
- [Quickstart](./getting-started/quickstart.md)
- [ZK Concepts](./concepts/zero-knowledge.md)
- [Contract Reference](./contracts/overview.md)
- [SDK Reference](./sdk/overview.md)
- [FAQ](./faq.md)

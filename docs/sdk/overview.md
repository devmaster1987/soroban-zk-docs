---
sidebar_position: 1
---

# SDK Overview

`soroban-zk-sdk` is the TypeScript client library for the Soroban ZK Toolkit. It handles proof generation, witness computation, Merkle tree management, and Soroban contract interaction.

## Installation

```bash
npm install soroban-zk-sdk
```

## Package structure

```
soroban-zk-sdk/
├── index.ts               # SorobanZKClient — main entry point
├── circuits/
│   ├── IdentityProver.ts
│   ├── MembershipProver.ts
│   ├── RangeProver.ts
│   └── TransferProver.ts
├── merkle/
│   └── MerkleTree.ts      # Poseidon-based Merkle tree
├── crypto/
│   └── poseidon.ts        # Poseidon hash (BN254 field)
└── soroban/
    └── client.ts          # Soroban RPC wrapper
```

## SorobanZKClient

The main client class connects to a Stellar network and a deployed verifier contract.

```ts
import { SorobanZKClient } from "soroban-zk-sdk";

const client = new SorobanZKClient({
  network: "testnet",           // "testnet" | "mainnet" | "futurenet"
  verifierContract: "CXXX...", // Contract address
  rpcUrl: "https://...",        // Optional — defaults to Soroban Labs RPC
});
```

## Provers

Each circuit has a dedicated Prover class:

```ts
import { IdentityProver }   from "soroban-zk-sdk/circuits";
import { MembershipProver } from "soroban-zk-sdk/circuits";
import { RangeProver }      from "soroban-zk-sdk/circuits";
import { TransferProver }   from "soroban-zk-sdk/circuits";
```

All provers expose a `prove(privateInputs, publicInputs)` method that returns `{ proof, publicSignals }`.

## Merkle utilities

```ts
import { MerkleTree } from "soroban-zk-sdk/merkle";

const tree = new MerkleTree(20); // depth 20
tree.insert(leaf);
const { root, pathElements, pathIndices } = tree.proof(leaf);
```

## See also

- [API Reference](./api-reference.md)
- [Quickstart](../getting-started/quickstart.md)

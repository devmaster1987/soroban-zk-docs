---
sidebar_position: 3
---

# Membership Circuit

The membership circuit proves that a committed leaf exists in a Merkle tree with a known root — without revealing which leaf or where it appears in the tree.

## Use case

Prove "my identity is in the allowlist" without revealing which identity you are.

## Circuit signals

### Private inputs

| Signal | Type | Description |
|--------|------|-------------|
| `leaf` | `Field` | The leaf value (hashed identity or commitment) |
| `pathElements` | `Field[levels]` | Sibling hashes along the Merkle path |
| `pathIndices` | `Field[levels]` | 0 = left sibling, 1 = right sibling |
| `nullifierSeed` | `Field` | Seed for nullifier derivation |

### Public inputs

| Signal | Type | Description |
|--------|------|-------------|
| `root` | `Field` | The Merkle tree root stored on-chain |
| `nullifier` | `Field` | `Poseidon(leaf, nullifierSeed)` |

## Configuration

The default tree depth is **20 levels** (~1 million leaves). You can compile a custom depth:

```bash
circom circuits/membership.circom \
  --r1cs --wasm --sym \
  -l node_modules \
  -o artifacts/ \
  -p=bn128 \
  -- --depth 10
```

## Proof generation (TypeScript)

```ts
import { MembershipProver } from "soroban-zk-sdk/circuits";
import { MerkleTree } from "soroban-zk-sdk/merkle";

// Build the Merkle tree from your allowlist
const tree = new MerkleTree(20);
for (const leaf of allowlist) {
  tree.insert(leaf);
}

const prover = new MembershipProver();

const { proof, publicSignals } = await prover.prove({
  leaf: myLeaf,
  pathElements: tree.proof(myLeaf).pathElements,
  pathIndices: tree.proof(myLeaf).pathIndices,
  nullifierSeed: BigInt(Date.now()),
  root: tree.root,
});
```

## On-chain verification

The contract checks that `root` matches the on-chain Merkle root stored in the `merkle-root-store` contract before invoking `verify_proof`.

## Constraints

- ~8,000 R1CS constraints (depth-20 tree)
- Proving time: ~1.2s (Node.js, M2 chip)
- Proof size: 256 bytes (Groth16)

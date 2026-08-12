---
sidebar_position: 3
---

# Merkle Trees and Membership Proofs

Merkle trees are a foundational data structure in ZK applications. They let you commit to a large set of values with a single hash, then prove membership of any element without revealing the rest of the set.

## Structure

A Merkle tree is a binary tree where:

- **Leaf nodes** contain hashed values (e.g., hashed identities or commitments).
- **Internal nodes** contain the hash of their two children.
- The **root** is a single 32-byte hash that commits to the entire dataset.

```
         root
        /    \
     h01      h23
    /   \    /   \
  h0   h1  h2   h3
  |    |   |    |
 L0   L1  L2   L3
```

## Merkle proofs

A **Merkle proof** (also called an *inclusion proof* or *authentication path*) proves that a leaf `L` is part of the tree with root `R`. It consists of the sibling hashes along the path from `L` to the root.

```
Proof for L1: [h0, h23]
```

A verifier recomputes the path:
1. `h01 = hash(h0, hash(L1))`
2. `root = hash(h01, h23)`

If the computed root matches `R`, the proof is valid.

## ZK membership proofs

In a ZK membership proof, the prover demonstrates knowledge of a leaf and its Merkle path without revealing which leaf. The Soroban ZK Toolkit uses a **Poseidon hash** function inside the Circom membership circuit because Poseidon is ZK-friendly (much cheaper than SHA-256 in circuit constraints).

### Circuit inputs

| Input | Visibility | Description |
|-------|-----------|-------------|
| `leaf` | Private | The element being proven |
| `pathElements[]` | Private | Sibling hashes along the path |
| `pathIndices[]` | Private | Left/right indicators at each level |
| `root` | Public | The committed Merkle root |

### On-chain flow

1. The contract stores (or accepts) a Merkle root.
2. A user generates a membership proof off-chain using the SDK.
3. The contract verifies the proof — confirming the user's leaf is in the tree without learning which leaf.

## See also

- [Membership circuit reference](../circuits/membership.md)
- [SDK overview](../sdk/overview.md)

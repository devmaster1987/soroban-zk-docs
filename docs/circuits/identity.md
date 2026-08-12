---
sidebar_position: 2
---

# Identity Circuit

The identity circuit proves knowledge of a secret that hashes to a public commitment. It is the simplest circuit in the toolkit and the best starting point for learning the system.

## Use case

Prove "I know the secret behind this commitment" without revealing the secret.

## Circuit signals

### Private inputs

| Signal | Type | Description |
|--------|------|-------------|
| `secret` | `Field` | The secret value (e.g., a passphrase hashed to a field element) |
| `nullifierSeed` | `Field` | A unique seed used to derive the nullifier |

### Public inputs

| Signal | Type | Description |
|--------|------|-------------|
| `commitment` | `Field` | `Poseidon(secret)` — must match the on-chain commitment |
| `nullifier` | `Field` | `Poseidon(secret, nullifierSeed)` — used for replay prevention |

## Circuit logic

```circom
template Identity() {
    signal input secret;
    signal input nullifierSeed;
    signal output commitment;
    signal output nullifier;

    component commitHasher = Poseidon(1);
    commitHasher.inputs[0] <== secret;
    commitment <== commitHasher.out;

    component nullifierHasher = Poseidon(2);
    nullifierHasher.inputs[0] <== secret;
    nullifierHasher.inputs[1] <== nullifierSeed;
    nullifier <== nullifierHasher.out;
}
```

## Proof generation (TypeScript)

```ts
import { IdentityProver } from "soroban-zk-sdk/circuits";

const prover = new IdentityProver();

const { proof, publicSignals } = await prover.prove({
  secret: BigInt("0x" + Buffer.from("my-secret").toString("hex")),
  nullifierSeed: BigInt(Date.now()),
});

// publicSignals[0] = commitment
// publicSignals[1] = nullifier
```

## Constraints

- ~1,200 R1CS constraints
- Proving time: ~0.3s (Node.js, M2 chip)
- Proof size: 256 bytes (Groth16)

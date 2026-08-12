---
sidebar_position: 2
---

# Quickstart

Generate and verify a ZK proof on Stellar Testnet in under 5 minutes.

## 1. Install the SDK

```bash
npm install soroban-zk-sdk
```

## 2. Download circuit artifacts

```bash
npx soroban-zk-sdk download-artifacts --circuit identity
```

## 3. Generate an identity proof

```ts
import { IdentityProver } from "soroban-zk-sdk/circuits";

const prover = new IdentityProver();

// Private inputs — never leave your machine
const witness = {
  secret: "my-secret-passphrase",
  nullifierSeed: "unique-per-action-seed",
};

// Public inputs
const publicInputs = {
  commitment: prover.computeCommitment(witness.secret),
};

const proof = await prover.prove(witness, publicInputs);
console.log("Proof generated:", proof);
// { pi_a: [...], pi_b: [...], pi_c: [...] }
```

## 4. Verify on-chain

```ts
import { SorobanZKClient } from "soroban-zk-sdk";

const client = new SorobanZKClient({
  network: "testnet",
  // Contract address of the deployed Groth16 verifier
  verifierContract: "CXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
});

const result = await client.verifyProof({
  circuit: "identity",
  proof,
  publicInputs,
});

console.log("Verified:", result.success); // true
```

## 5. What just happened

1. You computed a commitment to your secret (a hash that hides the secret).
2. The `IdentityProver` built a Groth16 proof that you know the preimage of that commitment.
3. The Soroban verifier contract checked the proof — without ever seeing your secret.

## Next steps

- [First proof — full tutorial](./first-proof.md)
- [SDK API reference](../sdk/api-reference.md)
- [Contract reference](../contracts/groth16-verifier.md)

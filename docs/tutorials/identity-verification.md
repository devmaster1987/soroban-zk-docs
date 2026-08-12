---
sidebar_position: 1
---

# Tutorial: Identity Verification End-to-End

This tutorial builds a complete identity verification system: users commit their identity off-chain, and a Soroban contract verifies their ZK proof before granting access.

## Architecture

```
User                    SDK (off-chain)            Soroban
 │                           │                        │
 │── generate commitment ───>│                        │
 │<─ commitment hash ────────│                        │
 │── store commitment ───────────────────────────────>│
 │                           │                        │
 │── request access ─────────────────────────────────>│
 │<─ challenge (nullifierSeed) ──────────────────────-│
 │── prove(secret, seed) ───>│                        │
 │<─ proof + nullifier ──────│                        │
 │── verify_proof ────────────────────────────────────>│
 │<─ access granted ──────────────────────────────────│
```

## Step 1 — Deploy and initialize the verifier

```bash
# Deploy the verifier contract
soroban contract deploy \
  --wasm contracts/groth16-verifier.wasm \
  --network testnet \
  --source admin-keypair

# Register the identity circuit
soroban contract invoke \
  --id <VERIFIER_CONTRACT> \
  --network testnet \
  --source admin-keypair \
  -- register_circuit \
  --circuit_id identity \
  --vk_json "$(cat artifacts/identity.vk.json)"
```

## Step 2 — User registers their identity

```ts
// register-identity.ts
import { IdentityProver } from "soroban-zk-sdk/circuits";
import { SorobanZKClient } from "soroban-zk-sdk";

const prover = new IdentityProver();

// User's secret — store this securely, never share
const secret = BigInt("0x" + Buffer.from("alice-passphrase-v1").toString("hex"));

// Compute commitment (safe to publish on-chain)
const commitment = prover.computeCommitment(secret);

// Store commitment in the identity registry contract
const client = new SorobanZKClient({
  network: "testnet",
  verifierContract: process.env.VERIFIER_CONTRACT!,
});

const txHash = await client.invoke({
  contract: process.env.IDENTITY_REGISTRY!,
  method: "register",
  args: { commitment: commitment.toString() },
});

console.log("Identity registered. Tx:", txHash);
console.log("Commitment:", commitment.toString());
```

## Step 3 — User proves their identity

```ts
// prove-identity.ts
import { IdentityProver } from "soroban-zk-sdk/circuits";
import { SorobanZKClient } from "soroban-zk-sdk";

const prover = new IdentityProver();
const client = new SorobanZKClient({
  network: "testnet",
  verifierContract: process.env.VERIFIER_CONTRACT!,
});

// Fetch a fresh nullifier seed from the contract (prevents replay)
const { nullifierSeed } = await client.query({
  contract: process.env.IDENTITY_REGISTRY!,
  method: "get_challenge",
  args: {},
});

const secret = BigInt("0x" + Buffer.from("alice-passphrase-v1").toString("hex"));

const { proof, publicSignals } = await prover.prove(
  { secret, nullifierSeed: BigInt(nullifierSeed) },
  { commitment: process.env.COMMITMENT! }
);

// publicSignals[0] = commitment, publicSignals[1] = nullifier
const result = await client.verifyProof({
  circuit: "identity",
  proof,
  publicInputs: {
    commitment: publicSignals[0],
    nullifier: publicSignals[1],
  },
});

if (result.success) {
  console.log("Access granted! Tx:", result.txHash);
} else {
  console.error("Proof rejected:", result.error);
}
```

## Step 4 — Contract-side logic

The identity registry contract:

1. Receives the `verify_proof` call via cross-contract call.
2. Checks the nullifier has not been used (replay prevention).
3. Stores the nullifier.
4. Grants access (emits an event, sets a flag, or mints a token).

## Security considerations

- **Nullifier reuse** — each `nullifierSeed` should be unique per action (use a contract-issued nonce).
- **Secret storage** — the user's secret must never leave their device unencrypted.
- **Commitment binding** — store commitments in contract storage, not in events, to make them harder to front-run.

## Next steps

- [SDK API reference](../sdk/api-reference.md)
- [Membership circuit](../circuits/membership.md) — extend this to prove group membership

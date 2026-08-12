---
sidebar_position: 3
---

# Your First ZK Proof

This tutorial walks through generating a Groth16 proof from scratch and verifying it on Soroban Testnet.

## What you will build

An identity commitment scheme where you:
1. Commit to a secret off-chain.
2. Generate a ZK proof of knowledge of that secret.
3. Verify the proof on-chain via the Groth16 verifier contract.

## Setup

```bash
mkdir zk-demo && cd zk-demo
npm init -y
npm install soroban-zk-sdk dotenv
npx soroban-zk-sdk download-artifacts --circuit identity
```

Create a `.env` file:

```
STELLAR_NETWORK=testnet
VERIFIER_CONTRACT=CXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
SECRET=my-super-secret-value
```

## Step 1 — Compute a commitment

```ts
// commit.ts
import { poseidon } from "soroban-zk-sdk/crypto";

const secret = process.env.SECRET!;
const commitment = poseidon([BigInt("0x" + Buffer.from(secret).toString("hex"))]);
console.log("Commitment:", commitment.toString());
// Store this commitment on-chain or share it publicly
```

Run:

```bash
npx ts-node commit.ts
```

## Step 2 — Generate the proof

```ts
// prove.ts
import "dotenv/config";
import { IdentityProver } from "soroban-zk-sdk/circuits";

const prover = new IdentityProver({ artifactsDir: "./artifacts" });

const proof = await prover.prove(
  { secret: process.env.SECRET! },
  { commitment: process.env.COMMITMENT! }
);

// Save proof to disk
import { writeFileSync } from "fs";
writeFileSync("proof.json", JSON.stringify(proof, null, 2));
console.log("Proof saved to proof.json");
```

## Step 3 — Verify on-chain

```ts
// verify.ts
import "dotenv/config";
import { SorobanZKClient } from "soroban-zk-sdk";
import proof from "./proof.json";

const client = new SorobanZKClient({
  network: process.env.STELLAR_NETWORK as "testnet" | "mainnet",
  verifierContract: process.env.VERIFIER_CONTRACT!,
});

const result = await client.verifyProof({
  circuit: "identity",
  proof,
  publicInputs: { commitment: process.env.COMMITMENT! },
});

if (result.success) {
  console.log("✓ Proof verified on Testnet!");
} else {
  console.error("✗ Verification failed:", result.error);
}
```

## Step 4 — Run the full flow

```bash
# 1. Get your commitment
npx ts-node commit.ts
# Commitment: 12345678...

# 2. Export commitment and generate proof
export COMMITMENT=12345678...
npx ts-node prove.ts

# 3. Verify on Testnet
npx ts-node verify.ts
```

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| `Artifacts not found` | Missing `.wasm` / `.zkey` | Re-run `download-artifacts` |
| `Invalid proof` | Wrong public inputs | Ensure `COMMITMENT` matches the one used during proof generation |
| `Contract not found` | Wrong contract address | Check `VERIFIER_CONTRACT` in `.env` |

## Next steps

- [Identity verification tutorial](../tutorials/identity-verification.md)
- [SDK API reference](../sdk/api-reference.md)
